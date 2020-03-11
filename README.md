# Understanding_Poseidon
Understanding CyberRoots Poseidon
## https://github.com/CyberReboot/poseidon/blob/master/bin/gen_pcap_manifest
Generate a compressed CSV of MAC/IP/file mappings from pcaps
#### Requirements:
1. argparse: Is a full command-line argument parser tool, and handles non-optional arguments.
2. glob:     The glob module is used to retrieve files/pathnames matching a specified pattern.
3. os, sys, subprocess: used to relay commands on the terminal and read the output
4. ipaddress:
#### Objectives achieved:
1. def get_pcap_mac_ips(pcap_dirs): Objective of this function is to first check whether pcap files exist in pcap_dirs. Followed by this it runs the command: tshark -T fields -r pcap -s 256 -e 'eth.src' -e 'eth.dst' -e 'ipv6.src_host' -e 'ipv6.dst_host' -e 'ip.src' -e 'ip.dst' for any particular pcap file present in the pcap_dirs.
Once completed this will output to us: eth_src_str, eth_dst_str, ipv6_src, ipv6_dst, ipv4_src, ipv4_dst
* netaddr.EUI(eth_src_str, dialect=netaddr.mac_unix_expanded) is used to expand the given eth_src to mac_unix type
ip_src = ipaddress.ip_address(src_ip_str) will output to us, an IPv4Address or IPv6Address object depending on the IP address passed as argument.
This is done for eth_dst as well as ip_dst.
So final output would be: ((eth_src, ip_src), (eth_dst, ip_dst))

2. def gen_manifest(pcap_pairs, csv_output): Objectives of this function is to create a type of log of all the pcaps being read in the form of: {'eth': str(eth), 'ip': str(ipa), 'pcap': pcap} every line is for a new pcap

3. def main():Generate a compressed CSV of MAC/IP/file mappings from pcaps. It calls the above two functions sequentially to create the compressed csv file.

## https://github.com/CyberReboot/poseidon/blob/master/bin/poseidon
Poseidon, an application that leverages software defined networks (SDN) to acquire and then feed network traffic to a number of machine learning techniques. For more info visit: https://github.com/CyberReboot/poseidon
This the bash file which allows for all the commands to interface with poseidon.
Usage: poseidon [option]
Options:

    -a,  api           get url to the Poseidon API
    -c,  config        display current configuration info
    -d,  delete        delete Poseidon installation (uses sudo)
    -e,  shell         enter into the Poseidon shell, requires Poseidon to already be running
    -h,  help          print this help
    -i,  install       install Poseidon repo (uses sudo)
    -l,  logs          display the information logs about what Poseidon is doing
    -p,  pipette       run pipette to support volos
    -r,  restart       restart the Poseidon service (uses sudo)
    -s,  start         start the Poseidon service (uses sudo)
    -S,  stop          stop the Poseidon service (uses sudo)
    -u,  update        update Poseidon repo, optionally supply a version or 'master' (uses sudo)
    -v,  viz/visualize get url to visualize Poseidon with CRviz
    -V,  version       get the version installed"
    
    
#### Requirements:
1. Installs: curl, docker, docker-compose, jq, wget, git. On its own using the commands:
`
for req_cmd in curl docker docker-compose jq wget git ; do
            $req_cmd --help > /dev/null || die "$req_cmd must be installed"
    done
`
#### Objectives:
1. Command Line Options:
-a: Gives a port_map of all connections sitting under Poseidon at this point this allows Poseidon assess whether it is running or not.
-c: Gives out "$POSEIDON_PREFIX"/opt/poseidon/poseidon.config basically gives out Poseidon Configuration
-d: Deletes
-h: Shows help
-i -z: Installs Poseidon Latest Release: curl -s https://api.github.com/repos/cyberreboot/poseidon/releases/latest
-i: curl -L https://github.com/cyberreboot/poseidon/tarball/"$2" here $2 could be say, "v0.11.0" or version name
Followed by this -i will update current Poseidon by changing:
a. "$POSEIDON_PREFIX"/opt/poseidon/config/poseidon.config
b. "$POSEIDON_PREFIX"/opt/poseidon/bin/gen_pcap_manifest
c. "$POSEIDON_PREFIX"/opt/poseidon
-l: Returns logs of all the pcaps assessed by Poseidon: `tail -n 100 -f /var/log/poseidon/poseidon.log`
-p: Runs Pipette
-r: Restarts Poseidon
-e: Starts Posedion shell
-s: Starts Posedion
-S: Stops Poseidon
-u: Updates Poseidon by changing:
a. /usr/local/bin/gen_pcap_manifest from https://raw.githubusercontent.com/CyberReboot/poseidon/master/bin/gen_pcap_manifest
b. /usr/local/bin/poseidon from https://raw.githubusercontent.com/CyberReboot/poseidon/master/bin/poseidon
-v: Visualise using crvix, shows port map on crvix 
-V: Returns running version

2. check_files:
Checks whether different files/directories exist or not. (/var/log/faucet, /var/log/faucet/faucet.log, /var/log/faucet/faucet_exception.log, /var/log/faucet/faucet.log, /var/log/faucet/faucet_exception.log, /var/log/poseidon, /var/log/poseidon/poseidon.log, "$POSEIDON_PREFIX"/etc/faucet/, "$POSEIDON_PREFIX"/etc/faucet/faucet.yaml.

3. parse_config:
Configures and redies Pipette

4. run_pipette:
Runs pipette and complete its installation
What is Pipette? https://github.com/CyberReboot/pipette
Pipette is a tool that allows users to multiplex SDN coprocessing by implementing transparent L3 NAT. Pipette does this by creating a virtual network behind your coprocessor port and then acting as the SDN controller of that network. Packets are seamlessly switched to their appropriate destination using Ryu.
## https://github.com/CyberReboot/poseidon/blob/master/config/poseidon.config
Tells Poseidon about defferent parameters and their configurations for use.
#### Objectives:
Sets up configurations for: Poseidon, NetworkML, Faucet, Bcf (did not find), Volos, Pipette.
## https://github.com/CyberReboot/poseidon/blob/master/config/volos_config.yaml
Sets up the configuration of Volos:
Installs: https://github.com/yeasy/simple-web, simple-web is a simple web to show the source and destination IP addresses of the received requests information, written in python.

#### Objectives:
Starts Volos.

## https://github.com/CyberReboot/poseidon/blob/master/healthcheck/hc.py
Performs basic health checkup on Poseidon, it is linked with the main GitHub Rep @ https://github.com/CyberReboot/poseidon
#### Requirements: https://github.com/CyberReboot/poseidon/blob/master/healthcheck/requirements.txt
flask and healthcheck

##  https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/dashboards.yaml
Configures dashboard settings.

## https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/docker-compose-faucet.yaml
Configures and sets up faucet

## https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/docker-compose-monitoring.yaml
Configures and sets up prometheus, for Monitoring system & time series database

## https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/docker-compose.yaml
Sets and configures RabbitMQ (https://hub.docker.com/r/faucet/event-adapter-rabbitmq-pi) 

## https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/faucet.rules.yml
Configures faucet rules: 
#### Objectives:
1. Convert OF stats to rates
2. Sum hosts learned on VLANs
3. Sum hosts learned on ports
4. Convert Port stats to rates (rate for 1m)

## https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/gauge.yaml
Sets up gauge

## https://github.com/CyberReboot/poseidon/blob/master/helpers/faucet/prometheus-docker-compose.yml
sets up prometheus as well scrape-intervals (default is 15s)

## https://github.com/CyberReboot/poseidon/tree/master/tests
have all the tests for checking each whether poseidon works or not.

# Poseidon (https://github.com/CyberReboot/poseidon/tree/master/poseidon)

## https://github.com/CyberReboot/poseidon/blob/master/poseidon/cli/cli.py
The main entry point for Poseidon Command Line
#### Objectives:
1. gets machine name: _get_name(endpoint)
2. gets machine mac: _get_mac(endpoint)
3. gets switch name: _get_switch(endpoint)
4. gets port name: _get_port(endpoint)
5. gets vlan data: _get_vlan(endpoint)
6. gets acls data:  _get_acls(endpoint)
7. gets ipv4 data: _get_ipv4(endpoint)
8. returns ipv4 subnet: _get_ipv4_subnet(endpoint)
9. Returns ether vendor: _get_ether_vendor(endpoint)
10. Returns reverse dns of ipv4: _get_ipv4_rdns(endpoint)
11. Returns reverse dns of ipv6: _get_ipv6_rdns(endpoint)
12. Returns ipv6 address:  _get_ipv6(endpoint)
13. Returns the ipv6 subnet: _get_ipv6_subnet(endpoint)
14. Returns controller type: _get_controller_type(endpoint)
15. Returns controller: _get_controller(endpoint)
16. Reutns boolean for whether to ignor current node or not: _get_ignored(endpoint)
17. Returns current state: _get_state(endpoint)
18. Returns following state: _get_next_state(endpoint)
19. Returns start time of node (Y-M-D-H-M-S):  _get_first_seen(endpoint)
20. Returns last seen time for chosen node (YMDHMS): _get_last_seen(endpoint)
21. Returns latest meta data: _get_newest_metadata(metadata)
22. Returns most recently evaluated role from latest meta_data {_get_newest_metadata(metadata)}: _get_role(endpoint)
23. Returns confidence from the most recently evaluated role from latest meta_data {_get_newest_metadata(metadata)}: _get_role_confidence(endpoint)
24. Returns behaviour of node from latest meta data: _get_behavior(endpoint)
25. Returns pcap labels from the latest meta data: _get_pcap_labels(endpoint)
26. Returns os of ipv4/ipv6: _get_ipv4_os(endpoint)/_get_ipv6_os(endpoint)
27. Returns the first and the last state followed by start time and duration of the endpoint/node: _get_prev_states(endpoint)
28. Gets the history of a particular endpoint: _get_history(endpoint)
#### Parser
1. __init__(self): Defining default items to be parsed etc.: 
Default fields: ['IPv4', 'IPv4 rDNS', 'Role', 'IPv4 OS', 'Ethernet Vendor', 'MAC Address', 'Pcap labels']
All fields: ['ID', 'MAC Address', 'Switch', 'Port', 'VLAN', 'IPv4',
            'IPv4 Subnet', 'IPv6', 'IPv6 Subnet', 'Ethernet Vendor', 'Ignored',
            'State', 'Next State', 'First Seen', 'Last Seen',
            'Previous States', 'IPv4 OS(p0f)', 'IPv6 OS(p0f)', 'Previous IPv4 OSes(p0f)',
            'Previous IPv6 OSes(p0f)', 'Role(NetworkML)', 'Role Confidence(NetworkML)', 'Previous Roles(NetworkML)',
            'Previous Role Confidences(NetworkML)', 'Behavior(NetworkML)', 'Previous Behaviors(NetworkML)',
            'IPv4 rDNS', 'IPv6 rDNS', 'SDN Controller Type', 'SDN Controller URI', 'History', 'ACL History',
            'Pcap labels']
2. completion(text, line, completions): Completes cut off senetences which have similar beginnings.
3. get_flags(text): Evaluating a command from terminal sequentially. By breaking them into boolean flags.
4. check_flags(self, flags, fields, sort_by=0, max_width=0, unique=False, nonzero=False, output_format='table', ipv4_only=True, ipv6_only=False, ipv4_and_ipv6=False): It evaluates the boolean returned by the above function and returns valid, fields, sort_by, max_width, unique, nonzero, output_format, ipv4_only, ipv6_only, ipv4_and_ipv6.
5. display_ip_filter(fields, ipv4_only, ipv6_only, ipv4_and_ipv6): Returns filtered_fields of ip addresses they include: IPV4_FIELD, IPV6_FIELD
6. display_results(self, endpoints, fields, sort_by=0, max_width=0, unique=False, nonzero=False, output_format='table', ipv4_only=True, ipv6_only=False, ipv4_and_ipv6=False): Looks up different fields and their values as well as defines their id.
`
fields_lookup = {'id': (GetData._get_name, 0),
                         'mac': (GetData._get_mac, 1),
                         'mac address': (GetData._get_mac, 1),
                         'switch': (GetData._get_switch, 2),
                         'port': (GetData._get_port, 3),
                         'vlan': (GetData._get_vlan, 4),
                         'ipv4': (GetData._get_ipv4, 5),
                         'ipv4 subnet': (GetData._get_ipv4_subnet, 6),
                         'ipv6': (GetData._get_ipv6, 7),
                         'ipv6 subnet': (GetData._get_ipv6_subnet, 8),
                         'ethernet vendor': (GetData._get_ether_vendor, 9),
                         'ignored': (GetData._get_ignored, 10),
                         'state': (GetData._get_state, 11),
                         'next state': (GetData._get_next_state, 12),
                         'first seen': (GetData._get_first_seen, 13),
                         'last seen': (GetData._get_last_seen, 14),
                         'previous states': (GetData._get_prev_states, 15),
                         'ipv4 os': (GetData._get_ipv4_os, 16),
                         'ipv4 os\n(p0f)': (GetData._get_ipv4_os, 16),
                         'ipv6 os': (GetData._get_ipv6_os, 17),
                         'ipv6 os\n(p0f)': (GetData._get_ipv6_os, 17),
                         'previous ipv4 oses': (GetData._get_prev_ipv4_oses, 18),
                         'previous ipv4 oses\n(p0f)': (GetData._get_prev_ipv4_oses, 18),
                         'previous ipv6 oses': (GetData._get_prev_ipv6_oses, 19),
                         'previous ipv6 oses\n(p0f)': (GetData._get_prev_ipv6_oses, 19),
                         'role': (GetData._get_role, 20),
                         'role\n(networkml)': (GetData._get_role, 20),
                         'role confidence': (GetData._get_role_confidence, 21),
                         'role confidence\n(networkml)': (GetData._get_role_confidence, 21),
                         'previous roles': (GetData._get_prev_roles, 22),
                         'previous roles\n(networkml)': (GetData._get_prev_roles, 22),
                         'previous role confidences': (GetData._get_prev_role_confidences, 23),
                         'previous role confidences\n(networkml)': (GetData._get_prev_role_confidences, 23),
                         'behavior': (GetData._get_behavior, 24),
                         'behavior\n(networkml)': (GetData._get_behavior, 24),
                         'previous behaviors': (GetData._get_prev_behaviors, 25),
                         'previous behaviors\n(networkml)': (GetData._get_prev_behaviors, 25),
                         'ipv4 rdns': (GetData._get_ipv4_rdns, 26),
                         'ipv6 rdns': (GetData._get_ipv6_rdns, 27),
                         'sdn controller type': (GetData._get_controller_type, 28),
                         'sdn controller uri': (GetData._get_controller, 29),
                         'history': (GetData._get_history, 30),
                         'acl history': (GetData._get_acls, 31),
                         'pcap labels': (GetData._get_pcap_labels, 32)}
`
removes rows that are all zero or 'NO DATA'
delete columns with no data

6. display_table(column_count, max_width, matrix): Displays fields from above in a tabulated manner
7. display_csv(matrix): use StringIO to create a file like object as a string so that we can use the built in csv contructs so as to properly handle edge/corner cases

#### Poseidon_Shell:
1. __init__(self, *args, **kwargs): 
This defines the Poseidon Shell Completely:
`
            ['role active-directory-controller', 'role administrator-server',
            'role administrator-workstation', 'role business-workstation',
            'role developer-workstation', 'role gpu-laptop', 'role pki-server',
            'role unknown', 'state active', 'state inactive', 'state known',
            'state unknown', 'state mirroring', 'state abnormal', 'state shutdown',
            'state reinvestigating', 'state queued', 'state ignored',
            'behavior normal', 'behavior abnormal', 'os windows', 'os freebsd',
            'os linux', 'os mac', 'history', 'version', 'what', 'where', 'all']
`
2. show_all(self, arg, flags): Show all things on the network
`
valid, fields, sort_by, max_width, unique, nonzero, output_format, ipv4_only, ipv6_only, ipv4_and_ipv6
`
3. show_state(self, arg, flags): Show all things on the network that match a state
`
Fields = ['Switch', 'Port', 'State',
                  'Ethernet Vendor', 'Mac', 'IPv4', 'IPv6']
`
4. show_behavior(self, arg, flags):Show all things on the network that match a behavior
5. show_os(self, arg, flags): Show all things on the network operating on same os
6. show_what(self, arg, flags): Find out what something is using the command: what_is(arg):
`
        WHAT [IP|MAC|ID]
        WHAT 10.0.0.1
        WHAT 18:EF:02:2D:49:00
        WHAT 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
`
7. show_history(self, arg, flags): Find out the history of something on the network:
`
        HISTORY [IP|MAC|ID]
        HISTORY 10.0.0.1
        HISTORY 18:EF:02:2D:49:00
        HISTORY 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
`
8. show_acls(self, arg, flags): Find out the history of ACLs of something on the network
`
        ACLS [IP|MAC|ID]
        ACLS 10.0.0.1
        ACLS 18:EF:02:2D:49:00
        ACLS 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
`
9. show_where(self, arg, flags): Find out where something is
`
        WHERE [IP|MAC|ID]
        WHERE 10.0.0.1
        WHERE 18:EF:02:2D:49:00
        WHERE 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
`
10. help_show(self): Help command
11. task_set(self, arg, flags): Set the state of things on the network:
`
        SET [IP|MAC|ID] [STATE]
        SET 10.0.0.1 INACTIVE
        SET ABNORMAL UNKNOWN (TODO - NOT IMPLEMENTED YET)
        SET 18:EF:02:2D:49:00 KNOWN
        SET 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293 SHUTDOWN
`
12. task_ignore(self, arg, flags): Ignore something on the network:
`
        IGNORE [IP|MAC|ID]
        IGNORE 10.0.0.1
        IGNORE 18:EF:02:2D:49:00
        IGNORE 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
        IGNORE INACTIVE
`
13. task_clear(self, arg, flags): Stop ignoring something on the network:
`
        CLEAR [IP|MAC|ID]
        CLEAR 10.0.0.1
        CLEAR 18:EF:02:2D:49:00
        CLEAR 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
        CLEAR IGNORED
`
14. task_remove(self, arg, flags): Remove something on the network until it's seen again:
`
        REMOVE [IP|MAC|ID]
        REMOVE 10.0.0.1
        REMOVE 18:EF:02:2D:49:00
        REMOVE 8579d412f787432c1a3864c1833e48efb6e61dd466e39038a674f64652129293
        REMOVE IGNORED
        REMOVE INACTIVE
`
## 


## 
