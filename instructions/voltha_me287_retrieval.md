# Instructions for Retrieving ME 287 Information from ONTs via VOLTHA CLI

## Prerequisites
- Access to Kubernetes cluster
- kubectl configured with appropriate permissions
- Access to VOLTHA pods
- ONTs connected and registered in the system

## Step 1: Connect to Kubernetes Cluster

1. Verify your Kubernetes context:
```bash
kubectl config current-context
```
Example output:
```
minikube
```

2. List available pods in the VOLTHA namespace:
```bash
kubectl get pods -n voltha
```
Example output:
```
NAME                                    READY   STATUS    RESTARTS   AGE
voltha-5f7d8c6b4d-abc12                1/1     Running   0          2d
voltha-etcd-0                          1/1     Running   0          2d
voltha-kafka-0                         1/1     Running   0          2d
```

## Step 2: Access VOLTHA CLI

1. Connect to the VOLTHA pod:
```bash
kubectl exec -it voltha-5f7d8c6b4d-abc12 -n voltha -- /bin/bash
```
Expected response: You will be inside the pod's shell

2. Access VOLTHA CLI:
```bash
voltha-cli
```
Example output:
```
Welcome to VOLTHA CLI
Type 'help' for available commands
voltha>
```

## Step 3: Retrieve ME 287 Information

1. List all ONUs:
```bash
devices list
```
Example output:
```
ID                                    TYPE    ADMIN STATE    OPER STATUS    CONNECT STATUS    REASON
0001aabbccddeeff                      onu     ENABLED        ACTIVE         REACHABLE         initial-mib-downloaded
0002aabbccddeeff                      onu     ENABLED        ACTIVE         REACHABLE         initial-mib-downloaded
```

2. For each ONU, get ME 287 information:
```bash
device 0001aabbccddeeff omci get 287
```
Example output:
```
ME 287 (ONU2-G) Information:
- Entity Instance: 0
- Attributes:
  - Attribute 1 (Managed entity ID): 287
  - Attribute 2 (Vendor ID): 0x0000
  - Attribute 3 (Version): 0x0001
  - Attribute 4 (Serial number): 0x0000000000000000
  - Attribute 5 (Traffic management option): 0x01
  - Attribute 6 (Priority queue): 0x01
  - Attribute 7 (Total T-CONT buffer number): 0x08
  - Attribute 8 (Total priority queue number): 0x08
  - Attribute 9 (Total traffic scheduler number): 0x08
  - Attribute 10 (Deprecated): 0x00
  - Attribute 11 (Total GEM port-ID number): 0x80
  - Attribute 12 (Sysuptime): 0x00000000
  - Attribute 13 (Connectivity capability): 0x01
  - Attribute 14 (Current connectivity mode): 0x01
  - Attribute 15 (QoS configuration flexibility): 0x01
  - Attribute 16 (Priority queue scale factor): 0x01
```

## Step 4: Save Output

1. To save the output to a file:
```bash
device 0001aabbccddeeff omci get 287 > me287_0001aabbccddeeff_20240312_1430.txt
```

2. Copy the file from the pod to your local machine:
```bash
kubectl cp voltha/voltha-5f7d8c6b4d-abc12:me287_0001aabbccddeeff_20240312_1430.txt ./me287_0001aabbccddeeff_20240312_1430.txt
```

## Step 5: Analyze Multiple ONTs

To retrieve ME 287 information for all ONTs in the system:

```bash
for device in $(devices list | grep "onu" | awk '{print $1}'); do
    device $device omci get 287 > me287_$device_$(date +%Y%m%d_%H%M%S).txt
done
```

## Troubleshooting

1. If device is not responding:
```bash
device 0001aabbccddeeff state
```
Possible outputs:
```
ADMIN STATE: ENABLED
OPER STATUS: ACTIVE
CONNECT STATUS: REACHABLE
REASON: initial-mib-downloaded
```
or
```
ADMIN STATE: DISABLED
OPER STATUS: FAILED
CONNECT STATUS: UNREACHABLE
REASON: device-not-found
```

2. To check OMCI communication status:
```bash
device 0001aabbccddeeff omci stats
```
Example output:
```
OMCI Statistics:
- Total messages sent: 100
- Total messages received: 100
- Success rate: 100%
- Last error: None
- Last error time: N/A
```

3. To reset OMCI communication:
```bash
device 0001aabbccddeeff omci reset
```
Expected response:
```
OMCI communication reset successfully
```

## Common Error Messages and Solutions

1. "Device not found":
   - Verify the device ID is correct
   - Check if the ONU is properly registered
   - Verify the device is in the correct state

2. "OMCI communication failed":
   - Check ONU connectivity
   - Verify OMCI channel is properly configured
   - Check for any network issues

3. "Permission denied":
   - Verify your Kubernetes permissions
   - Check VOLTHA CLI access rights
   - Ensure proper namespace access

## Notes
- ME 287 (ONU2-G) contains information about the ONU's capabilities and configuration
- The output format follows the G.988 standard specifications
- Make sure to have proper permissions before executing these commands
- Some commands might require additional parameters based on your specific VOLTHA configuration
- Always verify the device state before performing operations
- Keep track of timestamps for troubleshooting purposes

## Understanding ME 287 Information

When you execute `device 0001aabbccddeeff omci get 287`, you receive information about the ONU2-G (ME 287) managed entity. This ME is crucial as it provides information about the ONT's capabilities and supported features. Here's how to interpret the information:

### Key Attributes and Their Meaning

1. **Attribute 1 (Managed entity ID)**: 287
   - This confirms you're looking at ME 287 (ONU2-G)

2. **Attribute 2 (Vendor ID)**: 0x0000
   - Identifies the vendor of the ONT
   - Example values:
     - 0x0000: Generic
     - 0x0001: Vendor A
     - 0x0002: Vendor B

3. **Attribute 3 (Version)**: 0x0001
   - Indicates the version of the ONU2-G implementation
   - Higher versions may support additional features

4. **Attribute 5 (Traffic management option)**: 0x01
   - Indicates supported traffic management features
   - Values:
     - 0x01: Basic traffic management
     - 0x02: Advanced traffic management
     - 0x03: Full traffic management

5. **Attribute 7 (Total T-CONT buffer number)**: 0x08
   - Indicates how many T-CONT buffers are available
   - Each T-CONT represents a traffic container for upstream traffic

6. **Attribute 8 (Total priority queue number)**: 0x08
   - Shows the number of priority queues supported
   - Important for QoS (Quality of Service) configuration

7. **Attribute 9 (Total traffic scheduler number)**: 0x08
   - Indicates the number of traffic schedulers available
   - Used for traffic shaping and scheduling

8. **Attribute 11 (Total GEM port-ID number)**: 0x80
   - Shows the maximum number of GEM ports supported
   - GEM ports are used for data transmission

9. **Attribute 13 (Connectivity capability)**: 0x01
   - Indicates supported connectivity modes
   - Values:
     - 0x01: Basic connectivity
     - 0x02: Enhanced connectivity
     - 0x03: Full connectivity

### Determining Supported MEs

To determine which MEs are supported by the ONT, you need to:

1. Check the ONU's capabilities through ME 287 attributes
2. Query specific MEs to verify support

Example commands to check specific MEs:

```bash
# Check if ME 2 (ONU Data) is supported
device 0001aabbccddeeff omci get 2

# Check if ME 11 (PPTP Ethernet UNI) is supported
device 0001aabbccddeeff omci get 11

# Check if ME 45 (MAC Bridge Service Profile) is supported
device 0001aabbccddeeff omci get 45
```

### Interpreting ME Support

1. **Successful Response**:
   - If you get a valid response with attributes, the ME is supported
   - Example:
     ```
     ME 11 (PPTP Ethernet UNI) Information:
     - Entity Instance: 1
     - Attributes:
       - Attribute 1: 0x01
       ...
     ```

2. **Error Response**:
   - If you get an error like "ME not supported", the ONT doesn't support that ME
   - Example:
     ```
     Error: ME not supported
     ```

### Common MEs and Their Support Indications

1. **Basic MEs** (typically supported by all ONTs):
   - ME 2 (ONU Data)
   - ME 7 (Physical Path Termination Point)
   - ME 11 (PPTP Ethernet UNI)
   - ME 45 (MAC Bridge Service Profile)

2. **Advanced MEs** (may not be supported by all ONTs):
   - ME 84 (VLAN Tagging Filter Data)
   - ME 171 (Extended VLAN Tagging Operation)
   - ME 263 (ANI-G)
   - ME 287 (ONU2-G)

### Practical Example

Let's say you want to verify if an ONT supports VLAN tagging:

1. First, check ME 287 capabilities:
```bash
device 0001aabbccddeeff omci get 287
```

2. Then check specific VLAN-related MEs:
```bash
# Check VLAN Tagging Filter Data (ME 84)
device 0001aabbccddeeff omci get 84

# Check Extended VLAN Tagging (ME 171)
device 0001aabbccddeeff omci get 171
```

3. Interpret the results:
   - If both MEs return valid information, the ONT supports VLAN tagging
   - If you get "ME not supported" errors, the ONT doesn't support those features

### Notes on ME Support
- Not all ONTs support all MEs defined in G.988
- ME support can vary by vendor and ONT model
- Some MEs may be supported but with limited functionality
- Always verify ME support through actual queries rather than assuming based on ME 287 information

## References
- G.988 standard for ME 287 specifications
- VOLTHA documentation for CLI commands
- Kubernetes documentation for pod access 