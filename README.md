# sample project for issue 32282

start postgres

`docker-compose up -d`

start the spring boot project

it should hung on startup because of a flyway migration that performs a sleep(10000)

try to curl the liveness probe

curl -f http://localhost:9080/management/actuator/health/liveness

it will fail because the spring boot is not started yet.

In a kubernetes environment, the failing of the liveness probe will cause the pod to be killed and restarted.
Thus, any long db migration could not be properly executed as long as a liveness probe is defined.

In order for the migration to have a chance to complete, the liveness prove should return ok while the readiness probe should return ko.

See here for the definition of these probes:

https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

ko for the liveness probe will force the orchestrator to kill and restart the pod

ko for the readiness probe will force the orchestrator to not allow incoming traffic for this pod