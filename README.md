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

##


