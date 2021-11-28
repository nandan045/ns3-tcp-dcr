### Overview		<br/>
Congestion control is one of the most important feature of TCP. Traditional implementations of TCP assume packet loss mainly due to congestion.TCP was initially developed for wired networks, so if there are any packet losses most of them were due to congestion.
However in wireless this is not the case. In wireless networks significant number of packet losses could be due to channel errors. Hence assuming congestion to be the reason of packet loss every time and reducing the size of congestion window could be dangerous to the utilization of bandwidth in the network.
TCP DCR attempts to solve this issue. It increases the time at which fast retransmit/recovery algorithms are triggered by a time interval 𝜏. This gives a chance to the link layer to recover the lost packet if it is due to channel errors. If not it triggers the congestion algorithms. Because of increasing the time for triggering the congestion control algorithms, we are tring to differentiate between packet loss due to channel errors and packet loss due to congestion. So layer 4 and layer 3 of tcp/ip stack are totally independent of one another. One of the main advantages of doing this is that we are not reducing the congestion window size for packet loss due to channel errors. Thereby incresing throughput and utilizing the bandwidth to the maximum extent.
  
### Modifications in TCP  
1) Note the time of arrival of dupack.
2) Find the value of RTT and trigger the congestion control algorithm only when the packet is not retrieved by the receiver in the delayed time i.e. RTT ( Round Trip Time ).
3) If the packet failure is due to channel errors, then recover the packet using link layer retransmission.
4) If the packet is not recovered in the delayed time, then trigger congestion control algorithm. 

## Steps for Implementation
1) After understanding the concepts of TCP-DCR, refer the source code of ns3.
2) tcp-option.h and tcp-option.cc files need changes, since we are adding additional feature to ns-3. To make comparison of TCP without DCR and TCP with DCR, adding dcr option is necessary.
3) Refer tcp-socket.cc and tcp-socket.h files and understand all the modules and variables.
4) Our main motto here is to delay the response of the sender to duplicate acknowledgements, so modify dupak() module in the tcp-socket-base.cc file. 
5) Write a test script to make sure that the tcp-dcr feature is working fine.

## Formula for Delayed Response Time

  mdcrRetxThresh = mRetxThresh +  ((Window () * (m_rtt->GetEstimate ().GetMilliSeconds () * 1.0 / m_instRtt.GetMilliSeconds ()))/m_tcb->m_segmentSize)
  
  Window () - Limit the size of in-flight data by cwnd
  m_rtt - Round Trip Time
  m_instRtt - Instantaneous Round Trip Time
  m_tcb->segmentSize - segment size

## Use the following steps to build ns-3

1) Configure and build the repository using the following commands:
    Configuring ns-3 </br>
 - ./waf -d optimized configure </br>
    Build ns-3 using waf </br>
 - ./waf
2) Running test script to check the performance of TCP-DCR
 - ./waf --run="scratch/tcp-dcr-script.cc <options>=value"
   available options: </br>
   - nWifi = Number of wifi STA devices
   - tracing = Enable pcap tracing
   - DCR = Enable DCR
   - edt = EnergyDetectionThresholdThreshold energy of a received signal
   EXAMPLE:
    - ./waf --run="scratch/tcp-dcr-script.cc --dcr=false --edt=40.0 "
    - ./waf --run="scratch/tcp-dcr-script.cc --dcr=true --edt=40.0 "
    
 
### References
1) TCP DCR paper IEEE   https://ieeexplore.ieee.org/document/1492363/ 
2) https://www.nsnam.org/
3) https://www.nsnam.org/docs/models/html/tcp.html
