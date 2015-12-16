cmilter
=======

A milter to prevent e-mails from countries from which you don't wish to receive
e-mails.

Before installing:
    modify the blacklist hash table
    apt-get install libgeoip-dev
    choose your caching algorithm (look for $cache = in this code)
    Install CPAN Geo::IP

To install, simply put this script into /usr/local/etc/clmilter

If you are using systemd, run
    systemctl edit cmilter.service

Then copy and paste this content:
    [Unit]
    Description=Block e-mail by country
    After=network.target
    
    [Service]
    ExecStart=/usr/local/etc/cmilter local:/var/run/cmilter
    KillMode=process
    Restart=on-failure
    
    [Install]
    WantedBy=multi-user.target
    Alias=cmilter.service

Then run
    systemctl enable cmilter.service

If you are not using systemd, put these into /etc/rc.local:
    rm -f /var/tmp/cmilter
    /usr/local/etc/cmilter local:/var/tmp/cmilter

Finally put this into /etc/mail/sendmail.mc:
    INPUT_MAIL_FILTER(`cmilter', `S=local:/var/tmp/cmilter, F=T, T=S:4m;R:4m')dnl

And run
    service sendmail restart

