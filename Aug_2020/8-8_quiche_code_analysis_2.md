# quiche code analysis (2)

## server
- `flush_egress` is used to empty remain packets in the conn. It first use `quiche_conn_send` to write packets into buffer *out*, then it use `sendto` to send data to the peer. it will also set `timer.repeat` and call `ev_timer_again` function
- `mint_token` to generate token message, `validate_token` as it means. token only used in server. token is created by server, and return to client, and client use it to request
- `create_conn` used for establish connection
	- initial `conn_io`
	- `conn = quiche_accept()` creates a new server-side connection
	- `ev_init(&conn_io->timer, timeout_cb)` initial `timeout_cb` callback function
	- return `conn_io`
- `recv_cb` is a callback function, it will trigger when read data from sock
	
	- it is initialized in **main** function
	```c++
	ev_io_init(&watcher, recv_cb, sock, EV_READ);
	ev_io_start(loop, &watcher);
	```
	- enter while(1)
	- `recvfrom` is used to get data from sock
	- if `conn_io==NULL`, then `conn_io = create_conn()`
	- `quiche_conn_recv` is used to process packets received
	- if `quiche_conn_is_established`, then use `quiche_conn_stream_recv` to get data and store in buffer
	- while will break when (a)nothing from `recvfrom`, or (b)nothing from `quiche_conn_stream_recv`
- `timeout_cb` is a callback function, it will trigger after *timer. repeat*
	- it is initialized in `create_conn()`, which is in `recv_cb()`
	```c++
	ev_init(&conn_io->timer, timeout_cb);
	conn_io->timer.data = conn_io;
	```
	- its **timer** is set in `flush_egress`
	```c++
	conn_io->timer.repeat = t;
	ev_timer_again(loop, &conn_io->timer);
	```
	- `quiche_conn_on_timeout` used to process a timeout event
	- `flush_egress` is used
	- if `quiche_conn_is_closed`, then run `ev_timer_stop` and `free`


## client
- `flush_egress` used to send remain packet in `conn`, and set parameter
	- `quiche_conn_send` write data into buffer
	- `send` data to peer using sock
	- set `timer.repeat` and use `ev_timer_again`
- `recv_cb` is a callback function, trigger when read data from sock
	- initialized in **main**, use `ev_io_init`
	```c++
	ev_io_init(&watcher, recv_cb, conn_io->sock, EV_READ);
	ev_io_start(loop, &watcher);
	```
	- first enter into while(1)
	- `recv` used to read data from sock
	- `quiche_conn_recv` used to process QUIC packet
	- break when `errno==EWOULDBLOCK`
	- if `quiche_conn_is_closed`, then use `ev_break` to get out of loop
	- if `quiche_conn_is_established && !req_sent`, then use `quiche_conn_stream_send` to send *HTTP request* to server
	- if only `quiche_conn_is_established`, then use `quiche_conn_stream_recv` to get data from conn
	- `flush_egress`
- `timeout_cb` is a callback function
	
	- initialized in **main**
	```c++
	ev_init(&conn_io->timer, timeout_cb);
	conn_io->timer.data = conn_io;
	```
	- parameter is set in `flush_egress`
	```c++
	conn_io->timer.repeat = t;
	ev_timer_again(loop, &conn_io->timer);
	```
	- `quiche_conn_on_timeout` to process a timeout event
	- `flush_egress`
	- if `quiche_conn_is_closed`, then use `ev_break`