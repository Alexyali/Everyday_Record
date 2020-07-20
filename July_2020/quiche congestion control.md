# quiche congestion control
- congestion control is how to decide how much data the connection can send into network
- current draft recommends Reno as started, but can choose more advanced ones
- loss-based: Reno and CUBIC, delay-based: Vegas and BBR
- quiche provides a modular API to add a new congestion control module easily

## Reno
- cwnd: congestion window, the limit of how many bytes you send to the network
- ssthresh: a limit when slow start will stop
- 3 DUP ACK detection
- the `slow start` can be very aggressive, it doesn't stop until loss
- when packet loss is detected, it enters into `recovery` mode until the loss is recovered
- if cwnd > ssth, it entered into `congestion avoidance` mode, where cwnd grows slowly

## CUBIC
- CUBIC is the default congestion control in the Linux kernel
- Wmax is the value of CWND when congestion is detected
- it will reduce the CWND by 30% and then grow using a cubic function. it will approaching to aggresively in the beginning in the first but slowly converging to Wmax later.
- once it pass Wmax, it gows aggressively again

## HyStart++
- CUBIC only changed the way the CWND grows during congestion avoidance
- HyStart changes how the CWND is updated during slow start
	- RTT delay samples: when RTT over the threshold in `slow start`, it enters into `congestion avoidance`
	- ACK train: when ACK inter-arrival time over the threshold, it enters into `congestion avoidance`
- HyStart++ is a little different from HyStart
	- no ack train, only RTT sampling
	- add `Limited Slow Start` phase after slow start, it gows cwnd faster than congestion avoidance but slower than slow start.