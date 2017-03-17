Create by CC14KHMT
	creator:Vo Thanh Phong
		Huynh Hieu Trung
		Thai Ngoc Thanh
(See Run to know the running order)

Description:
This program provides the real-time temperature information detection service, 
which responsible for tracking the average temperature in three cities: Ho Chi Minh, New York, Miami.
The temperature information is taken from the website: http://www.accuweather.com

The system consists of four modules, including:
Sensor(HCMC_Client.java,MIAMI_Client.java,NYC_Client.java):
	These sensor will get the temperature live from http://www.accuweather.com and push it to our gateway via TCP.

Gateway(Server.java):
	The gateway will create individual threads, each thread in charge of receiving figure from a sensor.
	Then, each thread open a connection to CloudMQTT service via MQTT protocal and push the temperature on there. 
	Since the temperature update too fast, we have to sleed the thread everytime it successfully pushes the information.  
	Note: in each sensor,our team decided to append an approriate word (either: HCMC, NYC or MIAMI ) beside the temperature value to make the pulling process easier. 

CloudMQTT(tcp://m10.cloudmqtt.com:10203):
	This CloudMQTT service contain 3 topic(HCMTemp,NYCTemp,MIAMITemp), each topic will accept temparature from Gateway.

Analytic Application(Graph_HCM.java,GraphMI.java,GraphNY.java,MQTTPull.java):
	
	We have actually implemented 2 functions for this step: process the information and draw it.
	
	MQTTPull:This application will open a connection to Cloud MQTT and pull the info from the server. The figures will be sorted into different lists base on the topic they belong to. Then it will calculate and print the average, highest, lowest temparature in each city throughout the time. 
	
	Graph: Similarity, this application also open a connection and pull the info from the CloudMQTT server. It will then draw a graph.
Run order:
	Server.java -> the sensors you want to access(HCMC_Client.java,MIAMI_Client.java,NYC_Client.java) -> the corresspond application(Graph_HCM.java,GraphMI.java,GraphNY.java,MQTTPull.java)
Pros:
	The temperature get update very fast, down to one millisecond. 
	Many improvement, features can be implemented into the system, depend on the need of the user.
	
Cons:	
	The way we implement the server has a very big flaw, that is each thread will open a connection to the CloudMQTT server. As a result ,many problems arise such as synchronization, DDOS or message stalling if we had more sensor... Although we aware that this is a design mistake ,we have not find anyway to fix it yet.
	The info gathered by sensors is mostly unchange in short period of time, since temperature is rather stable. If we let the system run countinously in a day, the result maybe more observable.
	  