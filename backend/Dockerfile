FROM public.ecr.aws/docker/library/alpine:3
RUN adduser -D app
USER app
CMD sh -c 'while true; do echo "$(date) app alive"; sleep 30; done'
