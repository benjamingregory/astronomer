#
# Copyright 2016 Astronomer Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM astronomerinc/ap-airflow:0.7.0-rc.5-1.10.1 AS stage1
LABEL maintainer="Astronomer <humans@astronomer.io>"
ARG BUILD_NUMBER=-1
LABEL io.astronomer.docker=true
LABEL io.astronomer.docker.build.number=$BUILD_NUMBER
LABEL io.astronomer.docker.airflow.onbuild=true
# Install alpine packages
COPY packages.txt .
RUN cat packages.txt | xargs apk add --no-cache

FROM stage1 AS stage2
RUN mkdir -p /root/.ssh
ARG PRIVATE_RSA_KEY=""
RUN echo "${PRIVATE_RSA_KEY}" >> /root/.ssh/id_rsa
RUN chmod 600 /root/.ssh/id_rsa
RUN apk update && apk add openssh
RUN ssh-keyscan -H github.com >> /root/.ssh/known_hosts
# Install python packages
COPY requirements.txt .
RUN pip install --no-cache-dir -q -r requirements.txt

FROM stage1 AS stage3
# Copy requirements directory
COPY --from=stage2 /usr/lib/python3.6/site-packages/ /usr/lib/python3.6/site-packages/
