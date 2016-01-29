Bookworm images for Docker
----------------------------

This repository contains early work on Dockerizing Bookworm. Docker allows
self-standing containers that should run identically on any system you use.

The current design involves images for indexing, for running the api as an
daemon service, and for running the GUI. MySQL is run in it's own container,
and is accessed by a read-write user from `bw-indexing`, a read-only user from
`bw-api`, and not accessed by `bw-gui`.

Remembering that the Dockerfiles are still in progress, the general process
is:

- mysql is started as a Docker container, mounting a local drive for its
  space.
- `bw-indexing` is run with a local mount providing `jsoncatalog.txt`,
  `field_descriptions.json`, and `input.txt`. The container is meant to be
ephemeral, running only while it is needed, then powering down.
- `bw-api` is run as a daemon, with the port mapped through Docker. It only
  has read-only access (well not yet... soon)
- `bw-gui` is also run as a daemon.

## Building images

Since these images are in development and not yet in Docker Hub, you'll have
to build them from the Dockerfiles in this repo for `docker run` to be able to
run them. e.g. `cd /base-bw && docker build -t organisciak/base-bw .`

## Example

### Doc Prep

Here's an example of the process with the Congress docs. This collection and
the required files for Bookworm are described
[here](https://github.com/Bookworm-project/BookwormDB/blob/master/README.md#walkthrough).
To demonstrate Docker, we'll simply prepare the `jsoncatalog.txt`,
`field_descriptions.json`, and `input.txt` and move them to a local drive,
i.e `~/congress-files` below.

    git clone git://github.com/bmschmidt/congress_api
    cd congress_api
    python get_and_unzip_data.py
    python congress_parser.py
    mkdir ~/congress-files && cp *.{txt,json} ~/congress-files
    cd ../ && rm -rf congress_api

### Starting MYSQL

    # Create MySQL container with custom data drive
    mkdir my/drive/mysql-data
    # start Container
    docker run --name bw-mysql -v /my/drive/mysql-data:/var/lib/mysql \
	-v custom-conf:/etc/mysql/conf.d \
	-e MYSQL_ROOT_PASSWORD=my-secret-pw -d \
	mysql:5.5

This makes our local drive the data drive, adds any `.cnf` files in
custom-conf to the mysql configuration, and sets the root password. The `-d`
flag means it runs as a daemon, and the --name flag simply makes it easier to
refer to the running container.

Note, if Docker is configured to run in a place where you may run out of
space, you might want to map the /tmp drive to a local folder also. I haven't
tried this yet.

### Index Congress Files

`bw-indexing` looks for Bookworm's files in /bookworm-input, so we'll map
~/congress-files to that space in the container.

    docker run -v ~/congress-files/:/bookworm-input --link bw-mysql:bw-mysql \
        --rm --name bw-indexing organisciak/bw-indexing

Running this will automatically index the files to the linked database, then
remove the containeri (`--rm`). The --link command references the name of our mysql
container, and an alias for referring to it in the bw-indexing container. If
you named the mysql container something else earlier, for example 'puppies',
the link would be `--link puppies:bw-mysql`. Similar to how localhost refers to 127.0.0.1, an entry is added to /etc/hosts that
allows the container to find the MySQL database IP by referencing the alias.

For advanced needs you could run it without `--rm` and hop into the container
with `-i -t` and by adding `/bin/bash` to the end (to override the default
bookworm command that is run when a container is started).

### Start the API as a damon

    docker run -d -p 80:8080 --link bw-mysql:bw-mysql --name bw-api \
        organisciak/bw-api

This will run an API client on port 80 of the host machine. If you're running
this on your computer, go to localhost in your browser and you'll see it!
