# The Planets Earth by Vulnhub walkthrough

# Tools used

1. Netdiscover 
2. Nmap
3. 

##Step 1

Using netdiscover we can find the IP address of the target machine and begin our enumeration. We can use it as follows;
`sudo netdiscover` and you can look for the machine that is not the machine that you are currently using.
The output may look as follows;

<img width="660" height="462" alt="image" src="https://github.com/user-attachments/assets/fd732115-8763-4b6c-a6da-15a109eb13c6" />

As you can see we have found two machines the target and the current machine since in our set up we put them both on the same DHCP server.
Alternatively, you can use nmap with the range of IP addresses set during the set up.

<img width="955" height="339" alt="image" src="https://github.com/user-attachments/assets/92a6a7e2-433c-42cb-b90e-d9c615e0aed8" />

As you can see we have used the range we set during the setting up of the DHCP server and we have found one host on 10.38.1.113 just as we did with netdiscover.
