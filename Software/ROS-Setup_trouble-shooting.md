# Trouble shooting for ROS setup
## Open ROS ports for domain ID 72:
* standard ROS2
```
sudo ufw allow 25400:25500/udp
```
* micro-ros
```
sudo ufw allow 8888/udp
```
* check configuration
```
sudo ufw status
```
sould give something similar to
```
sudo ufw status        
Status: active

To                         Action      From
--                         ------      ----         
25400:25500/udp            ALLOW       Anywhere                  
8888/udp                   ALLOW       Anywhere                  
25400:25500/udp (v6)       ALLOW       Anywhere (v6)             
8888/udp (v6)              ALLOW       Anywhere (v6)        
```
