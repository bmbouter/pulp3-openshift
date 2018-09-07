# python-35-centos7
FROM centos/python-36-centos7

# TODO: Put the maintainer name in the image metadata
LABEL maintainer="Brian Bouterse <bbouters@redhat.com>"

# TODO: Rename the builder environment variable to inform users about application you provide them
# ENV BUILDER_VERSION 1.0

# TODO: Set labels used in OpenShift to describe the builder image
#LABEL io.k8s.description="Platform for building xyz" \
#      io.k8s.display-name="builder x.y.z" \
#      io.openshift.expose-services="8080:http" \
#      io.openshift.tags="builder,x.y.z,etc."

ENV DJANGO_SETTINGS_MODULE=pulpcore.app.settings
ENV PULP_SETTINGS=/opt/app-root/settings.yaml

RUN echo "SECRET_KEY: 'putyourreallylongsecretkeyhereandkeepitsecret'" > /opt/app-root/settings.yaml
RUN echo "DATABASES:" >> /opt/app-root/settings.yaml
RUN echo "  default:" >>/opt/app-root/settings.yaml
RUN echo "    ENGINE: django.db.backends.sqlite3" >> /opt/app-root/settings.yaml
RUN echo "    NAME: /opt/app-root/sqlite3.db" >> /opt/app-root/settings.yaml

# RUN pip install pulp_ansible
RUN pip install -e 'git://github.com/pulp/pulp#egg=pulpcore&subdirectory=pulpcore'
RUN pip install -e 'git://github.com/pulp/pulp#egg=pulpcore-plugin&subdirectory=plugin'
RUN pip install -e 'git://github.com/pulp/pulp_ansible#egg=pulp_ansible'

RUN pulp-manager makemigrations pulp_app
RUN pulp-manager makemigrations pulp_ansible

RUN rm /opt/app-root/settings.yaml
RUN rm /opt/app-root/sqlite3.db

RUN mkdir /opt/app-root/home

# TODO (optional): Copy the builder files into /opt/app-root
# COPY ./<builder_folder>/ /opt/app-root/

# TODO: Copy the S2I scripts to /usr/libexec/s2i, since openshift/base-centos7 image
# sets io.openshift.s2i.scripts-url label that way, or update that label
COPY ./s2i/bin/ /usr/libexec/s2i

# Drop the root user and make the /var/lib/pulp and /etc/pulp content owned by user 1001
# RUN chown -R 1001:1001 /opt/app-root/

# This default user is created in the openshift/base-centos7 image
USER 1001
WORKDIR /opt/app-root/home

# Set the default CMD for the image
CMD ["/usr/libexec/s2i/run"]
