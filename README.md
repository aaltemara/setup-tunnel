 Check and restart a sshuttle tunnel from a host (which only has access to a local network) to this host,
  routing traffic destined for a given remote network back through the tunnel.

  It is not necessary for the local-only host to be able to initiate connections to the host with access to the remote network
 This is intended to be executed from a local cron job as a non-privileged user

 Instructions:

 Context:
   - Host A is the host that has access to both remote and local networks
   - Host B is the host which only has access to the local network, which you want to give access to the remote network
 1. Copy this script to Host A.
 2. Install and configure sshuttle on Host B.
 3. Install 'nc' binary on both Host A and Host B. (netcat-openbsd on Ubuntu).
 4. Install and configure sshd on Host A and Host B.
 5. Configure SSH keys / passwordless login from Host A to Host B. It is not necessary for Host B to be able to initiate connections to Host A.
 6. Add the Host B user's SSH public key to the ~/.ssh/authorized_keys file of the Host A user.
 7. Test SSH passwordless login from Host A to Host B and back through the reverse tunnel.
      Example: This command should give the hostname of Host A
       ssh -R $SSHUTTLE_PORT:localhost:22 HOST_B_USER@HOST_B_IP ssh HOST_A_USER@localhost hostname
 8. Identify a host and port that can be contacted on the remote network to contact to test the connection.
 9. Execute this script: check-tunnel HOST_B_USER@HOST_B_IP REMOTE_NETWORK CHECK_HOST:CHECK_PORT
 10. Optionally, Update the config variables below

 If everything worked, you can now access the remote network from HOST B


Usage: check-tunnel [REMOTE_USER@]REMOTE_HOST_IP NETWORK CHECK_HOST:CHECK_PORT
