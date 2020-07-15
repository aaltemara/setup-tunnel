Check and restart a sshuttle tunnel from a host (which only has access to a local network) to this host, routing traffic destined for a given remote network back through the tunnel.

It is not necessary for the local-only host to be able to initiate connections to the host with access to the remote network. This is intended to be executed from a local cron job as a non-privileged user.

## Context:
- Host A is the host that has access to both remote and local networks
- Host B is the host which only has access to the local network, which you want to give access to the remote network

## Prerequisites
- Host A must be able to initiate connections to Host B. Host B does NOT have to be able to initiate connections to Host A.

## Instructions:
1. Copy this script to Host A.
2. Install and configure sshuttle on Host B.
3. Install 'nc' binary on both Host A and Host B. (netcat-openbsd on Ubuntu).
4. Install and configure sshd on Host A and Host B.
5. Configure SSH keys / passwordless login from Host A to Host B. It is not necessary for Host B to be able to initiate connections to Host A.
6. Add the Host B user's SSH public key to the ~/.ssh/authorized_keys file of the Host A user.
7. Add any additional DNS domains (that exist on the remote network) to the Host B's DNS resolver config
8. Test SSH passwordless login from Host A to Host B and back through the reverse tunnel.
      Example: This command should give the hostname of Host A
       ssh -R $SSHUTTLE_PORT:localhost:22 HOST_B_USER@HOST_B_IP ssh HOST_A_USER@localhost hostname
9. Identify a host and port that can be contacted on the remote network to contact to test the connection.
10. Execute this script: check-tunnel HOST_B_USER@HOST_B_IP REMOTE_NETWORK CHECK_HOST:CHECK_PORT
11. Optionally, Update the config variables below

 If everything worked, you can now access the remote network from HOST B


Usage: check-tunnel [REMOTE_USER@]REMOTE_HOST_IP NETWORK CHECK_HOST:CHECK_PORT


## Example:

### Situation:
You have a desktop (192.168.0.10) which is on a home network 192.168.0.0/24, and you have a laptop which has access to both the home network and a remote network 10.0.0.0/8. The laptop can iinitiate connects to the desktop, though not the reverse. Your username on the desktop is john. Your username on the laptop is jsmith. The local DNS domain name on the remote network is rnet.com
1. Install and configure sshuttle, ssh server, netcat on the desktop.
2. Install ssh server, netcat and this script on the laptop.
3. Ensure SSH passwordless login works from jsmith on the laptop to the john user on the desktop, and the desktop user (john's) public ssh key is added to the authorized_keys of the user on the laptop (jsmith).
4. Identify a known host 10.1.2.3 on the 10.0.0.0/8 network to be the 'check host:port'
5. Add rnet.com to the 'search' line on /etc/resolv.conf, or other methods for the specific OS.
6. Execute (as jsmith on the laptop): check-tunnel john@192.168.0.10 10.0.0.0/8 10.1.2.3:80

check-tunnel will then:
1. Bring up an ssh connection from the laptop to the desktop
2. Set up a reverse tunnel on the desktop from localhost:2222 to the laptop localhost:22
3. Execute sshuttle on the desktop, which causes network traffic from the desktop destined for 10.0.0.0/8 to be routed back through the laptop.

