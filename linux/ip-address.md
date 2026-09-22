# IP address

## What is an IP address?

An IP address, or Internet Protocol address, is a unique identifier assigned to each device on a network. It allows devices to communicate with each other by sending data packets. IP addresses come in two versions: IPv4 and IPv6, but for simplicity, we’ll focus on IPv4 in this blog.

### Public IP addresses: An overview

Public IP addresses are assigned to your network by your Internet Service Provider (ISP). These addresses are routable on the internet, meaning that they are used to identify your network in the global internet space. Here’s what you need to know:

- **Global Reach**: Public IP addresses are unique across the entire internet. They allow devices from different networks to communicate with each other.
- **Limited Supply**: There’s a finite number of IPv4 public addresses available, which is why IPv6 was introduced to expand the address space.
- **Visibility**: Devices with public IP addresses are accessible from anywhere on the internet, making them ideal for web servers, email servers, and other services that need to be reachable from outside networks.

#### Example of a public IP address

To find your public IP address, you can use a simple command in Ubuntu. Open your terminal and type:

```bash
curl ifconfig.me
```

**Output:**

```text
203.0.113.45
```

This command queries an external service to find your public IP address, which is visible to the outside world.

### Private IP addresses: An overview

Private IP addresses are used within local networks, such as your home or office network. They are not routable on the internet and are intended for internal communication. Here’s what you need to know:

- **Local Communication**: Devices within a local network use private IP addresses to communicate with each other. These addresses are not visible or accessible from the outside internet.
- **Address Ranges**: Private IP addresses fall within specific ranges defined by the Internet Assigned Numbers Authority (IANA). These ranges are:
  - **10.0.0.0 to 10.255.255.255**
  - **172.16.0.0 to 172.31.255.255**
  - **192.168.0.0 to 192.168.255.255**
- **Reuse**: Private IP addresses can be reused in different networks, allowing many devices to use the same address ranges without conflict.

#### Example of a private IP address

To find your private IP address on Ubuntu, you can use the following command in the terminal:

```bash
ip addr show
```

**Output:**

```text
2: eth0: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether 00:1a:2b:3c:4d:5e brd ff:ff:ff:ff:ff:ff
inet 192.168.1.10/24 brd 192.168.1.255 scope global dynamic eth0
valid_lft 86400sec preferred_lft 86400sec
```

Here, `192.168.1.10` is the private IP address assigned to your device.

## Key differences between public and private IP addresses

**Visibility**:

- **Public**: Accessible from anywhere on the internet.
- **Private**: Accessible only within the local network.

**Allocation**:

- **Public**: Assigned by your ISP.
  - **Private**: Assigned by your router or network administrator.

**Usage**:

- **Public**: Used for hosting services like websites, email servers, etc.
  - **Private**: Used for internal devices like computers, printers, and phones.

**Security**:

- **Public**: More vulnerable to attacks as it is exposed to the internet.
  - **Private**: Generally more secure as it is shielded behind a router or firewall.

### Real-world example: Home network setup

Imagine you’re setting up a home network. Your router will have a public IP address assigned by your ISP and private IP addresses for devices within your network. Here’s how it works:

1. **Router’s Public IP**: Your router uses this to connect to the internet. All traffic from your home network to the internet goes through this public IP.
2. **Devices’ Private IPs**: Each device (computer, smartphone, printer) gets a private IP address from the router. This allows them to communicate with each other locally.

### Conclusion

Understanding the difference between public and private IP addresses helps you manage network traffic, ensure security, and troubleshoot issues more effectively. While public IP addresses expose your network to the outside world, private IP addresses keep internal communication secure and contained. In my opinion, getting to grips with these concepts is one of those essential tech skills that pays off in countless ways, whether you’re a network administrator or just setting up your home Wi-Fi.
