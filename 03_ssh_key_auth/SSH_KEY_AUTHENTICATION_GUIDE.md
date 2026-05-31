# SSH Key Authentication for Cisco Network Devices

This guide shows you how to set up SSH key-based authentication for Cisco network devices, both manually and using Ansible automation.

## Prerequisites

- Ansible control node with `cisco.ios` collection installed
- Cisco IOS network devices reachable via SSH
- SSH access to network devices with admin privileges

## Method 1: Manual Setup

### Step 1: Generate SSH Key Pair

Generate a dedicated SSH key pair for network device authentication:

```bash
ssh-keygen
```

When prompted:
- **File location**: `/home/your_user/.ssh/cisco_rsa` (or your preferred path)
- **Passphrase**: Press Enter for no passphrase (or enter one for additional security)

### Step 2: Configure SSH Client

Add this line to your `/etc/ssh/ssh_config` file:

```
PubkeyAcceptedKeyTypes +ssh-rsa
```

### Step 3: Get the MD5 Hash of Your Public Key

Cisco IOS uses the MD5 hash of the SSH public key for authentication:

```bash
ssh-keygen -lf ~/.ssh/cisco_rsa.pub -E md5
```

Example output:
```
3072 MD5:f7:bf:98:6f:8e:82:f4:24:bb:26:0a:6d:f1:88:e2:e7 lawi@dev (RSA)
```

Note the hash without colons: `f7bf986f8e82f424bb260a6df188e2e7`

### Step 4: Configure Network Device

For each network device, create a user and configure SSH key authentication:

```bash
configure terminal
ip ssh pubkey-chain
username ansible_admin
key-hash ssh-rsa f7bf986f8e82f424bb260a6df188e2e7 lawi@dev
exit
exit
end
write memory
```

**Important:** Replace the hash and comment with your actual values from Step 3.

## Method 2: Ansible Automation

### Step 1: Generate SSH Key Pair

Same as Method 1, Step 1.

### Step 2: Configure SSH Client

Same as Method 1, Step 2.

### Step 3: Configure SSH Key Authentication Using Ansible

Run the Ansible playbook to automatically configure SSH key authentication:

**Default user (ansible_admin):**
```bash
ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/configure_ssh_key_auth.yml
```

**Custom user (e.g., lawi, network_admin, etc.):**
```bash
ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/configure_ssh_key_auth.yml -e "target_user=lawi"
```

This playbook will:
- Check if the user exists on network devices
- Create the user with privilege level 15 if it doesn't exist
- Calculate the MD5 hash of your SSH public key
- Configure the SSH public key using `key-hash` format

### Step 4: Verify Configuration

Check if the user and SSH key are properly configured:

**Check default user:**
```bash
ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/check_ssh_key_auth.yml
```

**Check custom user:**
```bash
ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/check_ssh_key_auth.yml -e "target_user=lawi"
```

### Step 5: Test SSH Key Authentication

Test SSH key authentication to your network devices:

**Connect as default user:**
```bash
ssh -i ~/.ssh/cisco_rsa ansible_admin@<device_ip_address>
```

**Connect as custom user:**
```bash
ssh -i ~/.ssh/cisco_rsa lawi@<device_ip_address>
```

## Cleanup and Maintenance

### Remove SSH Key Configuration

To remove SSH key authentication and the user:

**Remove default user:**
```bash
ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/remove_ssh_key_auth.yml
```

**Remove custom user:**
```bash
ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/remove_ssh_key_auth.yml -e "target_user=lawi"
```

This will:
- Remove the SSH public key from pubkey-chain
- Delete the user account from network devices

## Troubleshooting

### SSH Key Authentication Not Working

1. **Verify SSH key configuration:**
   ```bash
   ansible-playbook -i inventory/cisco_devices.ini 03_ssh_key_auth/check_ssh_key_auth.yml
   ```

2. **Check file permissions:**
   ```bash
   chmod 600 ~/.ssh/cisco_rsa
   chmod 644 ~/.ssh/cisco_rsa.pub
   ```

3. **Test SSH connection with verbose output:**
   ```bash
   ssh -v -i ~/.ssh/cisco_rsa ansible_admin@<device_ip_address>
   ```

4. **Verify the key hash matches:**
   ```bash
   # Get the hash from your public key
   ssh-keygen -lf ~/.ssh/cisco_rsa.pub -E md5

   # Check the configured hash on the device
   show running-config | include key-hash
   ```

### Common Issues

- **"Permission denied"**: Check that the user has appropriate privileges on the network device
- **"Incompatible ssh peer"**: Verify SSH client configuration includes `PubkeyAcceptedKeyTypes +ssh-rsa`
- **Key hash mismatch**: Ensure the MD5 hash in the configuration matches your public key

## How It Works

Cisco IOS stores SSH public keys using their MD5 hash rather than the full key string. This approach:

1. **Calculates the MD5 hash** of your SSH public key
2. **Stores the hash** in the configuration under `ip ssh pubkey-chain`
3. **Authenticates** by comparing the hash of the presented key during SSH login

The configuration format is:
```
ip ssh pubkey-chain
  username <username>
   key-hash ssh-rsa <md5-hash> <comment>
```

## Files Structure

```
ansible-devnet/
├── 03_ssh_key_auth/
│   ├── configure_ssh_key_auth.yml  # Main playbook for SSH key setup
│   ├── check_ssh_key_auth.yml      # Playbook to verify configuration
│   ├── remove_ssh_key_auth.yml     # Playbook to remove configuration
│   └── SSH_KEY_AUTHENTICATION_GUIDE.md # This guide
└── 6.ssh_key_auth.md              # Quick reference guide
```

## Additional Resources

- [Cisco IOS SSH Configuration Guide](https://www.cisco.com/c/en/us/support/docs/security/secure-shell-ssh/4145-ssh.html)
- [Ansible Cisco IOS Collection Documentation](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_config_module.html)
