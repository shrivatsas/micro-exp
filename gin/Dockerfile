# Multistage, minimized wtih 'Scratch' image
# https://www.cloudreach.com/en/resources/blog/cts-build-golang-dockerfiles/

FROM golang:alpine as builder
RUN mkdir /build 
ADD . /build/
WORKDIR /build 
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags '-extldflags "-static"' -o main .

FROM scratch
COPY --from=builder /build/main /app/
WORKDIR /app

EXPOSE 8000
CMD ["./main"]