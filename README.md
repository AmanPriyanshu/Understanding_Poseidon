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
