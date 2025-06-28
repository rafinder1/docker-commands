FROM alpine:latest

# Define a build-time argument
ARG VERSION

# Set it as an environment variable
ENV APP_VERSION=$VERSION

RUN echo "Building version: ${APP_VERSION}"

ENTRYPOINT ["echo"]

CMD ["Hello from CMD"]