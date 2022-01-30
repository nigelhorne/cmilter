cmilter
=======

A milter to prevent e-mails from countries from which you don't wish to receive
e-mails.

Before installing:

    modify the blacklist hash table in the source code
	apt-get install libgeoip-dev
    or
	yum install perl-IPC-System-Simple
    choose your caching algorithm (look for $cache = in the source code)
    Install Sendmail::PMilter from CPAN, the other needed modules will be installed automatically

To install, simply put this script into /usr/local/etc/cmilter

If you are using systemd, copy and paste this content into
/etc/systemd/system/cmilter.service

    [Unit]
    Description=Block e-mail by country
    After=network.target

    [Service]
    ExecStart=/usr/local/etc/cmilter local:/var/run/cmilter
    KillMode=process
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

Then run

    systemctl daemon-reload
    systemctl enable cmilter.service
    systemctl start cmilter.service

If you are not using systemd, put these into /etc/rc.local:

    rm -f /var/tmp/cmilter
    /usr/local/etc/cmilter local:/var/tmp/cmilter

Finally put this into /etc/mail/sendmail.mc:

    INPUT_MAIL_FILTER(`cmilter', `S=local:/var/tmp/cmilter, F=T, T=S:4m;R:4m')dnl

And run

    service sendmail restart
