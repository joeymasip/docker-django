FROM python:3.6

ENV PYTHONUNBUFFERED 1

RUN mkdir /application
WORKDIR "/application"

# Install python mysql client
RUN apt-get update \
    && apt-get -y install libmysqlclient-dev \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

ADD requirements.txt /application/
RUN pip install -r requirements.txt
