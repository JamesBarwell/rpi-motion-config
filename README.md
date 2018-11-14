rpi-motion-config
---

My configuration for setting up a Raspberry Pi motion detection camera.

Included:
* motion config
* nginx config
* install instructions

## Basic set up

SSH into the Raspberry Pi and run the following:

```
# Initial setup
sudo -i
apt-get update
apt-get upgrade

# Set up the camera
raspi-config
raspistill -o image.jpg

# Set up motion
apt-get install motion
modprobe bcm2835-v4l2
chown -R pi: /var/log/motion
systemctl enable motion

# Copy the motion.conf over to /etc/motion/motion.conf

# Set up nginx
apt-get install nginx

# Copy the motion.nginx.conf over to /etc/nginx/sites-available/motion
htpasswd -c motion_auth camera
mv motion_auth /etc/nginx/
ln -s /etc/nginx/sites-available/motion /etc/nginx/sites-enabled/
systemctl reload nginx

# Useful commands
tail -f /var/log/motion/motion.log
sudo service motion status
```

The motion live stream server should be available at the Raspberry Pi's IP address on port 80.

## Encrypted set up

```
# Copy the motion-secure.nginx.conf over to /etc/nginx/sites-available/motion
# Hash out the TLS secure server

systemctl reload nginx

# Follow the instructions here: https://github.com/JamesBarwell/acme-tiny-run
# Register a certificate

cd /etc/ssl/private
openssl dhparam -dsaparam -out /etc/ssl/private/dhparam.pem 4096

# Un-hash the TLS secure server

systemctl reload nginx
```
