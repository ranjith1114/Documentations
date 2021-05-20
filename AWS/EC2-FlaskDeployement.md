## EC2 Flask application deployement
[Reference: https://www.youtube.com/watch?v=MAsp90tQGOA](https://www.youtube.com/watch?v=MAsp90tQGOA)

### Login to AWS account and open VPC
1. Select **Your VPC's** on the left side menu
2. Check for **default VPC**, if exists we can navigate to EC2 console
3. Else click on **Actions**
4. Select Create **VPC**
5. Choose create **default VPC**
6. Now Open EC2 console

### EC2 console
1. Click on **launch instance**
2. Select an AMI(Amazon machine image) - **Ubuntu 18.04**
3. In next window select instance type - **t2 micro**
4. Click on next **configure instance** details, Ensure default VPC should be selected in Network 
5. Click on next: **Add storage** if required
6. Click on next: **Add tags**
7. Click on next: **Configure Security Group**, Add Rule, choose HTTP
8. Click on **Review and Launch** 
9. Click on **launch**
10. It will pop up a new screen where you choose Create a new key pair option.
And enter Key name.
Click on Download **Key pair**, It will download the **.pem file** to your local machine.
Then Click on **Launch Instances**
11. Click on the **instance ID** it will take us to the Instance window once the Instance State become **Running**, we can start use the instances. At the place of name, we can give our instance a name.
Once the instance become running
12. On the left side menu select Elastic Ips under Network & Security
13. Click on **Allocate Elastic IP address**
14. Select that ip and click **Actions** and then **Associate Elastic IP address**
15. In the Instance place Select the Created EC2 Instance choose the Its Private IP and click on Associate.
16. Rename Elastic IP to avoid conflicts
17. Now go back to the instance window and select the ec2 instance. 
18. Ensure Status checks become 2/2 checks
19. Select your instance and click on Connect
20. In the following options choose **SSH Client**

### Open Terminal(MAC) Command Prompt(Windows)
1. Locate your private key file 
2. Goto file location where .pem file has downloaded
3. Change pem file permissions **chmod 400 <pem file name>** --> You can also have these steps in opened window
4. Connect to instance using its public DNS copy example line - **ssh -i "python_poc.pem" ubuntu@ec2-3-94-234-195.compute-1.amazonaws.com** 
5. Paste it in terminal
6. For each time the instance restarted the public IP (ec2-3-94-234-195.compute-1.amazonaws.com)
Given by AWS change but elastic IP won’t change.
7. You have succesfully logged in with SSH client

### Install Required packages
1. Update package manager **sudo apt-get update**
2. Install python2 **sudo apt-get python3**
3. Check python version **python -V**
4. Install python package **sudo apt install python3-pip**
5. To get pip3 **where is pip3**
6. Install flask **pip3 install flask**
7. Install Nginx webserver server **sudo apt-get install nginx**
8. Install gunicorn3 **sudo apt-get install gunicorn3**

### Create flask application
1. Create a folder **mkdir flaskapp**
2. Enter into that folder **cd flaskapp**
3. Add app.py **vim app.py**
    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def helloIndex():
        return 'Hello World from Python Flask!'
4. Save and quit app.py (Esc + :wq)
5. If you want to run application standalone mode
    Add the below code in app.py
    if __name__=="__main__":
        app.run(host='0.0.0.0',port=8080)
6. Run python3 app.py
7. Copy public IP from EC2 console and paste in browser (http://3.94.234.195:8080/)
8. If this link takes much time that means port settings are not correct
9. Open EC2 console 
10. Open Elastic Ips, In Inboud rule tab add new rule
    Custom TCP -> port number(8080)
11. Open browser again, flask application will be deployed successfully

### Running with WSGI server
1. Goto root directory → cd
2. Open nginx folder → cd /etc/nginx/sites-enabled/
3. Create a file named flaskapp → sudo vim flaskapp
	server{
       		listen 80;
	        server_name 18.232.199.238;
		location / {
                	proxy_pass http:127.0.0.1:8000;
        		}
	}
    listen 80 id used for listening 80 port
    proxy_pass is for defining localhost port
4. Save the file using esc enter :wq
5. Restart nginx server → **sudo service nginx restart**
6. Goto root directory → **cd**
7. Enter into flaskapp folder → **cd flaskapp**
8. Now, run **gunicorn3 app:app** in terminal
9. Open publicip:portnumber in browser Ex: http://18.232.199.238:80

 ### To run application in secured way
 1. Goto security groups, and select security group
 2. In inbound rule, add rule
 3. Add tcp with port number 443
 4. Try to open in browser by adding https...
 5. Change listen 80->443 in flaskapp file
 6. Now you can access application