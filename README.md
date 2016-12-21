The main goal of the keepalived project is to add a strong & robust
keepalive facility to the Linux Virtual Server project.
It implements a multilayer TCP/IP stack checks. Keepalived implements
a framework based on three family checks : Layer3, Layer4 & Layer5. 
This framework gives the daemon the ability of checking a LVS server 
pool states. Keepalived can be sumarize as a LVS driving daemon.

Keepalived implementation is based on an I/O multiplexer to handle a 
strong multi-threading framework. All the events process use this I/O
multiplexer.

Keepalived is free software, Copyright (C) Alexandre Cassen.
See the file COPYING for copying conditions.


OPENSSL TOOLKIT LICENCE EXCEPTION

In addition, as the copyright holder of Keepalived,
I, Alexandre Cassen, <acassen@linux-vs.org>,
grant the following special exception: 

        I, Alexandre Cassen, <acassen@linux-vs.org>, explicitly allow
        the compilation and distribution of the Keepalived software with
        the OpenSSL Toolkit.

Make a little change to tool keepalived, based on v1.2.2 fullnat version. in order to deal with clear_diff_services() fails.
check_daemon.c


```
        /* Processing differential configuration parsing */
        if (reload) {
                int try_times = 1;
                while (try_times < 10) {
                        if (clear_diff_services() == 1)
                                break;
                        log_message(LOG_ERR, "clear diff services error, have tried %d times !", try_times);
                        try_times ++;
                }
                if (try_times == 10)
                        stop_check(0);
        }
```

ipwrapper.c

```
/* IPVS cleaner processing, choose check_data or old_check_data */
int
clear_services(int iscd)
{
        element e;
        list l;
        if (iscd == 1) {
                l = check_data->vs;
        }
        else {
                l = old_check_data->vs;
        }
        virtual_server *vs;
        real_server *rs;

        for (e = LIST_HEAD(l); e; ELEMENT_NEXT(e)) {
                vs = ELEMENT_DATA(e);
                rs = ELEMENT_DATA(LIST_HEAD(vs->rs));
                if (!clear_service_vs(check_data->vs_group, vs))
                        return 0;
        }
        return 1;
}
```
