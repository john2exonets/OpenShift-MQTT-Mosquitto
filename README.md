# OpenShift-MQTT-Mosquitto
Run Mosquitto MQTT Broker on Red Hat OpenShift

__To Use__

	$ oc create -f mosquitto.yaml
	configmap/mosquitto-config created
	deployment.apps/mosquitto created
	service/mosquitto created

This will start up the toke/mosquitto Docker container, with just port 1883 open. You can modify the Deployment part of the mosquitto.yam manifest file to include SSL support (you will need to add your SSL certs to the mosquitto.conf and mosquitto.acl parts of the ConfigMap.)

To connect to the Mosquitto Broker, you will need to know the 'External-IP' address and port:

	$ oc get svc
	NAME        TYPE           CLUSTER-IP    EXTERNAL-IP                 PORT(S)          AGE
	mosquitto   LoadBalancer   172.30.5.13   172.29.175.9,172.29.175.9   1883:32342/TCP   3m

In this case, it is 172.29.175.9:32342. If you do not have MQTT clients that can specify a port besides 1883, you will want to do an 'oc port-forward' command like so:

	$ oc port-forward mosquitto-54dc8c9b8c-k4wk4 1883:1883
	Forwarding from 127.0.0.1:1883 -> 1883
	Forwarding from [::1]:1883 -> 1883

The 'mosquitto-54dc8c9b8c-k4wk4' being the name of the Pod.  Keep in mind that this method bypasses the Service Load Balancer and goes straight to the pod.

When you want to connect to the Mosquitto broker, be sure to specify the port:

	$ node node_modules/mqtt/mqtt.js pub -h $(minishift ip) -t "test/blatt" -m '{"test": "blatt"}' -p 32342 -v

More info at: https://bigredstack.com/run-mosquitto-mqtt-broker-on-red-hat-openshift/ 
