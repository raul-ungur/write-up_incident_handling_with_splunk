 Investigative Report

Lab : Incident handling with  splunk
We will follow the Cyber kill Chain Model and map the attacker's activity in each phase during this Investigation 

Reconnaissance : 

To begin the investigation, I sought to identify any reconnaissance attempts directed at our web server, imreallynotbatman.com. Working from the assumption that an attacker had interacted with our infrastructure, I decided to analyze the network traffic logs.

I then ran the following query in Splunk to determine which log sources contained traces of our domain:

index=botsv1 imreallynotbatman.com

The results indicated that the primary sources were Suricata, stream:http, fortigate_utm, and iis. To isolate the inbound web traffic, I focused on the stream:http source:

index=botsv1 imreallynotbatman.com sourcetype=stream:http

Examining the "Fields" panel on the left, I looked at the `src_ip` field. Two main IP addresses emerged: 40.80.148.42 and 23.22.63.114. The first IP accounted for a significantly higher percentage of the logs than the second—a strong indicator of intense and suspicious activity. To confirm my suspicions, I clicked on the IP 40.80.148.42 to filter the results and analyzed the `User-Agent` and `URI` fields. I observed that the traffic from this IP contained numerous anomalous requests to our server, suggesting an automated scanning attempt.

To further validate the scanning activity, I decided to check whether the Intrusion Detection System (IDS) had triggered any alerts. I then queried the Suricata logs, filtering for the suspicious IP:

index=botsv1 imreallynotbatman.com src=40.80.148.42 sourcetype=suricata

By analyzing the fields related to the alerts (such as alert.signature), I found definitive confirmation: Suricata had triggered an alert for an attempted exploitation of the CVE-2014-6271 vulnerability (also known as Shellshock). Furthermore, by examining the HTTP logs, I was able to identify that our web server was running the Joomla CMS and that the attacker was using the Acunetix scanner to automate their requests.

Observations regarding the server IP: I also identified the IP of the target server by analyzing the logs, which corresponds to 192.168.250.70.
