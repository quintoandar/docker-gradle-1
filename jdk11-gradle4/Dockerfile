FROM openjdk:11-jdk

CMD ["gradle"]

ENV GRADLE_HOME /opt/gradle
ENV GRADLE_VERSION 4.10.3

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        unzip \
        wget && \
    rm -rf /var/lib/apt/lists/*

ARG GRADLE_DOWNLOAD_SHA256=8626cbf206b4e201ade7b87779090690447054bc93f052954c78480fa6ed186e
RUN set -o errexit -o nounset \
    && echo "Downloading Gradle" \
    && wget --no-verbose --output-document=gradle.zip "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" \
    \
    && echo "Checking download hash" \
    && echo "${GRADLE_DOWNLOAD_SHA256} *gradle.zip" | sha256sum --check - \
    \
    && echo "Installing Gradle" \
    && unzip gradle.zip \
    && rm gradle.zip \
    && mv "gradle-${GRADLE_VERSION}" "${GRADLE_HOME}/" \
    && ln --symbolic "${GRADLE_HOME}/bin/gradle" /usr/bin/gradle \
    \
    && echo "Adding gradle user and group" \
    && groupadd --system --gid 1000 gradle \
    && useradd --system --gid gradle --uid 1000 --shell /bin/bash --create-home gradle \
    && mkdir /home/gradle/.gradle \
    && chown --recursive gradle:gradle /home/gradle \
    \
    && echo "Symlinking root Gradle cache to gradle Gradle cache" \
    && ln -s /home/gradle/.gradle /root/.gradle

# Create Gradle volume
USER gradle
VOLUME "/home/gradle/.gradle"
WORKDIR /home/gradle

RUN set -o errexit -o nounset \
    && echo "Testing Gradle installation" \
    && gradle --version
