# Secure WireGuard VPN with SSH Access

This project sets up a WireGuard VPN server with a contained SSH access point. The SSH container is fully isolated and serves primarily as a secure entry point to establish SSH tunnels. Both services are compatible with x64 and ARM processors.

## Configuration

You can configure the services by editing the `.env` file:

```
# SSH proxy settings
SSH_PORT=2222    # Port to expose the SSH service on the host
SSH_USER=admin   # Username for SSH authentication
SSH_PASSWORD=password  # Password for SSH authentication

# WireGuard settings
WG_PORT=51820    # Port for WireGuard connections
WG_PEERS=1       # Number of peer configurations to generate
WG_INTERNAL_SUBNET=10.13.13.0  # Internal subnet for WireGuard
```

## Usage

1. Start the services:

```bash
docker-compose up -d
```

2. Connect to the VPN:
   - The WireGuard configuration files will be generated in the `wireguard-data` volume
   - You can view the QR codes for mobile clients by checking the logs:
     ```bash
     docker-compose logs -f wireguard
     ```
   - Configuration files for peers are located at `/config/peer1/peer1.conf`, etc.
   - Get the configuration files with:
     ```bash
     docker-compose exec wireguard cat /config/peer1/peer1.conf
     ```
   - Import these configuration files into your WireGuard client
   - All your traffic will be routed through the VPN

3. Using the SSH tunnel (if needed):

```bash
# Connect to the SSH container
ssh ${SSH_USER}@localhost -p ${SSH_PORT}

# Create an SSH tunnel
ssh -L local_port:remote_host:remote_port -p ${SSH_PORT} ${SSH_USER}@localhost
```

## Security Features

This setup includes several security measures:

1. **SSH Container Isolation**:
   - Container runs with minimal Linux capabilities
   - SUDO access is disabled
   - Container has memory limits
   - No-new-privileges flag prevents privilege escalation
   - Users are contained within the SSH environment

2. **WireGuard VPN**:
   - All traffic routed through encrypted VPN tunnel
   - IP forwarding enabled for proper VPN functionality
   - Automatic peer configuration generation

## Multi-Architecture Support

This setup is compatible with both x64 and ARM processors, making it deployable on a wide range of devices from Raspberry Pi to standard servers.

## Security Notes

- Change the default username and password in the `.env` file
- All SSH sessions are contained within the isolated container
- The WireGuard setup provides the main VPN functionality
- SSH access is primarily intended for tunneling or emergency access 