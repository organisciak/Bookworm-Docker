## This is the base image that all Bookworm containers depend on.

FROM organisciak/bw-base
MAINTAINER Peter Organisciak <organisciak@gmail.com>
WORKDIR /BookwormDB
# TODO: We want to copy the cnf with the read-only user, not ~/.my.cnf
RUN cp ~/my.cnf bookworm.cnf && \
	## TODO TEMPORARILY ADD A DB LINE
	echo "database=BookwormDB">>bookworm.cnf && \
	ln -s /bookworm-input/jsoncatalog.txt && \
	ln -s /bookworm-input/field_descriptions.json && \
	ln -s /bookworm-input/input.txt
CMD ["bookworm", "build", "all"]
