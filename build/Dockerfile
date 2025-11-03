## Compile busybox from source, using a custom config to keep the final binary size down
FROM alpine AS builder

# Install all dependencies required for compiling busybox
RUN apk add gcc musl-dev make perl ncurses-dev

# Download busybox sources
RUN wget https://github.com/mirror/busybox/archive/refs/tags/1_36_1.tar.gz \
  && tar xzf 1_36_1.tar.gz \
  && mv busybox-1_36_1 /compile

WORKDIR /compile

# Copy the busybox build config. the config ensures we only build minimal dependencies into the image, keeping the size down
COPY .config .

# Compile and install busybox
RUN make && make install

# create a user that'll own the static files in the final iamge build
RUN adduser -D www


## Final image build: from scratch for a minimal image
FROM scratch
EXPOSE 80

# Copy the busybox static binary
COPY --from=builder /compile/_install/bin/busybox /

# Copy over the www user
COPY --from=builder /etc/passwd /etc/passwd
USER www

# assume /www as the dir that assets will be dropped into
WORKDIR /www

# Run busybox httpd
CMD ["/busybox", "httpd", "-f", "-vv", "-p", "80"]
