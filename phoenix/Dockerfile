# https://dev.to/shmink/multi-stage-docker-and-elixir-releases-1ce2

# Alternates
# https://dev.to/hlappa/development-environment-for-elixir-phoenix-with-docker-and-docker-compose-2g17
# https://raw.githubusercontent.com/revelrylabs/revelry_phoenix_app_template/main/Dockerfile

FROM elixir:1.10.4-alpine as builder

# install build dependencies
RUN apk add --no-cache build-base npm git python

# prepare build dir
WORKDIR /app

# install hex + rebar
RUN mix local.hex --force && \
    mix local.rebar --force

# set build ENV
ENV MIX_ENV=prod

# install mix dependencies
COPY mix.exs mix.lock ./
COPY config config
RUN mix do deps.get, deps.compile

# build assets
COPY assets/package.json assets/package-lock.json ./assets/
RUN npm --prefix ./assets ci --progress=false --no-audit --loglevel=error

COPY priv priv
COPY assets assets
RUN npm run --prefix ./assets deploy
RUN mix phx.digest

# compile and build release
COPY lib lib
# uncomment COPY if rel/ exists
# COPY rel rel
RUN mix do compile, release

# prepare release image
FROM alpine:3.9 AS app
RUN apk add --no-cache openssl ncurses-libs

WORKDIR /app

RUN chown nobody:nobody /app

USER nobody:nobody

COPY --from=builder --chown=nobody:nobody /app/_build/prod/rel/hello ./

ENV HOME=/app

EXPOSE 9000
CMD ["bin/hello", "start"]
