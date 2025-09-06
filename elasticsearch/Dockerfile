ARG ELK_VERSION
FROM elasticsearch:${ELK_VERSION} as builder

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt clean
RUN apt-get update -y
RUN apt-get install -y software-properties-common build-essential
RUN gcc --version
RUN apt-get update -y
RUN apt-get install -y make cmake pkg-config wget git

ENV JAVA_HOME=/usr/share/elasticsearch/jdk
ENV PATH=$JAVA_HOME/bin:$PATH

# Build coccoc-tokenizer
RUN echo "Build coccoc-tokenizer..."
WORKDIR /tmp
RUN git clone https://github.com/duydo/coccoc-tokenizer.git
RUN mkdir /tmp/coccoc-tokenizer/build
WORKDIR /tmp/coccoc-tokenizer/build
RUN cmake -DBUILD_JAVA=1 ..
RUN make install

ARG ELK_VERSION
COPY elasticsearch-analysis-vietnamese-${ELK_VERSION}.zip /usr/share/elasticsearch/

ARG ELK_VERSION
RUN cd /usr/share/elasticsearch && \
  elasticsearch-plugin install -b file:///usr/share/elasticsearch/elasticsearch-analysis-vietnamese-${ELK_VERSION}.zip  && \
  bin/elasticsearch-plugin install -b analysis-icu && \
  bin/elasticsearch-plugin list

FROM docker.elastic.co/elasticsearch/elasticsearch:$ELK_VERSION

ARG ELK_VERSION
ARG COCCOC_INSTALL_PATH=/usr/local
COPY --from=builder $COCCOC_INSTALL_PATH/lib/libcoccoc_tokenizer_jni.so /usr/lib

USER elasticsearch

EXPOSE 9200 9300
