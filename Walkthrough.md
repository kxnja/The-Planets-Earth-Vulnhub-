# The Planets Earth by Vulnhub walkthrough

# Tools used

1. Netdiscover 
2. Nmap
3. Gobuster
4. Cyberchef

# First key
## Step 1

Using netdiscover we can find the IP address of the target machine and begin our enumeration. We can use it as follows;
`sudo netdiscover` and you can look for the machine that is not the machine that you are currently using.
The output may look as follows;

<img width="660" height="462" alt="image" src="https://github.com/user-attachments/assets/fd732115-8763-4b6c-a6da-15a109eb13c6" />

As you can see we have found two machines the target and the current machine since in our set up we put them both on the same DHCP server.
Alternatively, you can use nmap with the range of IP addresses set during the set up.

<img width="955" height="339" alt="image" src="https://github.com/user-attachments/assets/92a6a7e2-433c-42cb-b90e-d9c615e0aed8" />

As you can see we have used the range we set during the setting up of the DHCP server and we have found one host on 10.38.1.113 just as we did with netdiscover.

Netdiscover might be a bit slower compared to nmap 

## Step 2

Run `nmap -sC -sV 10.38.1.113` to get the open ports and the services running on them.
The -sC part runs a default script against thos service to get more information on them.

From our scan we find 3 open ports ; port 80 , port 22 and port 443.
Under port 443 we find find two host names ; earth.local and terratest.earth.local.

<img width="734" height="480" alt="image" src="https://github.com/user-attachments/assets/22e3bcf6-2808-4367-9de1-89d947634c0b" />

Using nano we can save them to the etc/hosts file on our machine using the command ,`sudo nano /etc/hosts` click enter and then type in the ip address and the two host names, save the file using `ctrl+x` and then click `y` then enter.

## Step 3
Now head to your preferred browser and type in earth.local on the search bar. 
This takes you to a website that is titled Earth Secure Messaging Service.

<img width="955" height="847" alt="image" src="https://github.com/user-attachments/assets/860f88b8-87fa-44f6-b620-3a3a4cb06acf" />

You can try typing in a new message and entering a new key, this adds to the previous meassages just as I did.

<img width="955" height="847" alt="image" src="https://github.com/user-attachments/assets/2b059998-e236-4e6b-aaab-f80404fbd187" />

Now we can begin looking for hidden directories on the webpage and we can use gobuster to do this.

Enter the command ` gobuster dir -u http://earth.local/ -w /usr/share/wordlists/dirb/common.txt`
The `-u` command specifies the url to be scanned and the `-w` comand specifies the wordlist to be used to look for the possible directories.

<img width="710" height="372" alt="image" src="https://github.com/user-attachments/assets/f4fffa46-9f96-4199-84cb-b72214a54af6" />

From gobuster we find two directories; /admin and /cgi-bin/

Head over to earth.local/admin and here we find a login page but since we do not have any login credentials we have to look for them.

--
<img width="955" height="851" alt="image" src="https://github.com/user-attachments/assets/80c0d20f-27db-42f7-8d28-648043fd5dbc" />
--
<img width="955" height="845" alt="image" src="https://github.com/user-attachments/assets/50ae2075-cd2a-42dc-a3dc-9dc5b297a356" />

First we need to go to the other host name `terratest.earth.local` and using gobuster we can also find the hidden directories.

You can use the command  ` gobuster dir -u https://terratest.earth.local/ -k -w /usr/share/wordlists/dirb/common.txt`. As you can see the command is almost similar to the one used previously the only difference is that here we added another argument `-k` whic is used to skip the certification validation process since we are enumerating a https server on the 443 port.

<img width="730" height="458" alt="image" src="https://github.com/user-attachments/assets/6e5711a6-f28f-4b07-b869-2023f3b3f71b" />

Here we find a `robots.txt` file that we access by going back to the browser and searching `https://terratest.earth.local/robots.txt`.
In the robots.txt file we find another file called `testingnotes.txt`.

<img width="955" height="559" alt="image" src="https://github.com/user-attachments/assets/89f2880b-5af8-4481-aa26-d1084c9317b4" />

When we head to `https://terratest.earth.local/testingnotes.txt` we find that some developer had clumsily left off some notes on the network regarding the system.

<img width="955" height="848" alt="image" src="https://github.com/user-attachments/assets/1cb1d9bd-cc7c-46f7-b060-001633bcd851" />

From the notes we find that that the system use XOR encryption and the encyption key might be in the testdata.txt file.

## Step 4

We can use a tool called cyberchef to decrypt the file.
But first we need to install it.

So if you followed my procedure to set up the VM up and currently on the DNS server we need to change it to the bridged adapter so that the VM can connect to the internet from the host machine.

So go ahead and save the VM state and and then go over to network settings and change it to bridged adapter then start the VM again and install cyberchef.

Once it is installed save the VM state and go back to the network settings and change it back to internal network then start the VM again.

Now head to downloads and extract the zip file. open the extracted folder and open the HTML file.

Once opened and you are in the website do the following;
  1. Drag and drop `from hex` from the operations tab to the recipe tab.
  2. Search XOR and drag and drop it from the operations tab to the recipe tab.
  3. Under XOR set the key as the text the was found under the testdata.txt file `https://terratest.earth.local/testdata.txt`
     <img width="955" height="892" alt="image" src="https://github.com/user-attachments/assets/0a943b66-3e9a-4c1b-bb9a-3f82e8b6eaa3" />

  5. Also under XOR set it to UTF8.

Remember the messages we found on `Earth.local`, we will now copy and paste them one by one on the input page to see which one will give a viable output.

**SPOILER ALERT!!!!!!!!!!** The first two give gibberish the third is what we want.

This is the first.
<img width="955" height="888" alt="image" src="https://github.com/user-attachments/assets/54343ba3-904e-44d7-8b7a-2907620924c0" />

This is the second message.
<img width="955" height="848" alt="image" src="https://github.com/user-attachments/assets/7558658c-2801-44b4-a5f9-e11b0957b72f" />

This is the third.
<img width="955" height="846" alt="image" src="https://github.com/user-attachments/assets/1e1f0f85-f562-42c2-bd11-bf8aece01174" />

The third message gives an output of `earthclimatechangebad4humans` repeating several times.

## Step 5

Remember the login page we found on `earth.local/admin` and use the username given on `https://terratest.earth.local/testingnotes` ( terra ) use it to login and the password will be `earthclimatechangebad4humans` that we just decrypted. 

<img width="955" height="891" alt="image" src="https://github.com/user-attachments/assets/0cf83a5d-d0f0-4b82-abb2-ceeab8e3855c" />
---

<img width="955" height="886" alt="image" src="https://github.com/user-attachments/assets/21a60a35-6b06-4473-9fcc-d4225088fede" />

Once logged in we find a CLI (Command line Interface) and running a simple command like `ls` we can confirm that.

<img width="955" height="890" alt="image" src="https://github.com/user-attachments/assets/bc586b08-5a71-489c-8d1a-5a328d7198c6" />

Using the command `ls /var/earth_web` we find a `user_flag.txt` file.
We can use the cat command to open the file `cat /var/earth_web/user_flag.txt`.

Here we find the first flag.
<img width="955" height="893" alt="image" src="https://github.com/user-attachments/assets/43db2ecc-4ec5-4042-9b06-e48efb8b5b1b" />











