FROM python:3.11-slim-buster as prod

RUN sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list && rm -Rf /var/lib/apt/lists/* && apt-get update

RUN apt-get update && apt-get install -y \
  default-libmysqlclient-dev \
  gcc \
  pkg-config \
  openjdk-11-jdk \
  build-essential \
  && rm -rf /var/lib/apt/lists/*

# Using douban pipy mirror
RUN pip install -i https://mirrors.cloud.tencent.com/pypi/simple/ -U pip 
RUN pip config set global.index-url https://mirrors.cloud.tencent.com/pypi/simple

RUN pip install poetry==1.7

# Configuring poetry
RUN poetry config virtualenvs.create false

# Copying requirements of a project
COPY pyproject.toml /app/src/
WORKDIR /app/src

RUN poetry source add --priority=default aliyun http://mirrors.aliyun.com/pypi/simple/

# Installing requirements
RUN poetry install --only main
# Removing gcc
RUN apt-get purge -y \
  g++ \
  gcc \
  pkg-config \
  && rm -rf /var/lib/apt/lists/*

# Copying actual application
COPY . /app/src/
RUN poetry install --only main

CMD ["/usr/local/bin/python", "-m", "reworkd_platform"]

FROM prod as dev

RUN poetry install
