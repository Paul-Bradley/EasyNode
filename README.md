
Download VirtualBox and install it: https://www.virtualbox.org/wiki/Downloads or use another virtualisation platform of your choice if
you already have a preferred one.

Download the latest image (supplied as a split zipfile in parts, you need all the parts): https://github.com/Paul-Bradley/EasyNode/releases

NOTE: For release 0.1 initially a set of zips containing VirtualBox files was uploaded. I've now also added an OVA (Open Virtualisation Platform)
image packed into split zips, so it's easy to import this into VirtualBox, VMWare, HyperV or most other virtualisation platforms. You don't need
both sets of files, just one.

NOTE2: Especially if using the OVA format (but possibly also with the VirtualBox files, I haven't tested it on a fresh machine) you may find your
miner cannot connect (and you can't browse) to 127.0.0.1:9171 (p2pool). If this happens it's likely because the port forwarding set up when I
made the image isn't configured on your virtualisation software. For a guide to configuring port forwarding in VirtualBox or other virtualisation
software see http://www.howtogeek.com/122641/how-to-forward-ports-to-a-virtual-machine-and-use-it-as-a-server/ - you will need to forward
the appropriate port (9171 by default, but 9172/9174 if you reconfigure the image for p2pool networks 2 or 3) in your virtualisation software
so that the host (your computer) forwards traffic received on that port to the guest (the p2pool node VM), then you'll connect your miner or
browser to localhost (127.0.0.1) on the host but that traffic will be forwarded to p2pool running on the guest.


IF USING THE VIRTUALBOX FILES:

Unzip the image (including the folder) to the My Documents\VirtualBox VMs\ folder, so you have My Documents\VirtualBox VMs\P2Pool node\

Run virtualbox, and select "Add" from the "Machine" menu, browse to the unzipped files and select the "P2Pool node.vbox" file,
this will add the VM to your inventory and you can now select it and click "Start" to boot it.



IF USING THE OVA FILE:

Unzip the .ova file somewhere convenient then import it into your virtualisation software.

The image size is about 7GB as shipped, but will grow over time as the blockchains get bigger, it is set to a maximum 
size of 60GB and will grow dynamically to accomodate the extra data.

The image is set up with user p2pool (automatically logs in on boot) and password p2pool (this is the sudo password)

The coin wallets are set to only accept RPC from localhost for security. Vertcoin runs as a daemon in the background 
(to check it's status you can run vertcoind getinfo in a terminal window), MON and PLX run in the foreground as the 
QT (GUI) wallets.

When you connect a miner to the node, you connect to 127.0.0.1 (localhost) on the appropriate port (normally 9171
but see later in this HOWTO if you wish to mine on p2pool networks 2 or 3), using your VTC address as your username 
and with any password, for example your miner command line might be:

vertminer.exe -o stratum+tpc://127.0.0.1:9171 -u ViPBVm4sbXT38h9J23GkAWfNKiuPwVUYQX -p x

Please note that especially the first time you start the VM, it may take some time for p2pool to start listening,
since the blockchains of the wallets will need to be up to date (blockchains are downloaded and included in the image,
but only up to the date of the last release obviously), so be patient and allow it time to sync.

Your VTC is being mined directly to the address you use as username in your miner configuration (for example, an address 
on your usual VTC desktop wallet) and is paid in the generation transaction for the blocks (ie. it appears as immature in 
your VTC client, then takes 120 blocks to mature and become available for spending).

Your MON and PLX are mined directly into the wallets on the virtualbox image. Again, they are paid in the generation
transaction (you are solomining MON and PLX) and take 120 blocks to become spendable balance, at which time you can
send them in the usual way from the QT (GUI) wallets on the virtualbox image.

You can see the status of your node by browsing to http://127.0.0.1:9171 (or 9172/9174 if you changed the configuration
to mine on p2pool networks 2 or 3 as described later in this HOWTO) on your desktop.

Obviously if you run the image somewhere other than your mining rig or desktop PC, for example on another machine you have
spare, you will need to change 127.0.0.1 in both the miner configuration and the web URL to the appropriate IP on your LAN.


To update the wallets:

If a wallet update is required, open a terminal and do the following:

Vertcoin:

cd ~/vertcoin/src; git pull; make -f makefile.unix; sudo cp ./vertcoind /usr/local/bin; sudo /etc/init.d/vertcoind stop; sudo /etc/init.d/vertcoind start

Monocle:

cd ~/monocle/; git pull; qmake; make; sudo cp ./monocle-qt /usr/local/bin

Close the Monocle wallet, and re-open it using the link on the desktop

Parallaxcoin:

cd ~/parallaxcoin/; git pull; qmake; make; sudo cp ./parallaxcoin-qt /usr/local/bin

Close the Parallaxcoin wallet, and re-open it using the link on the desktop


By default, the image mined on the main VTC p2pool network (network 1, port 9171). If you have a low hashrate
and wish to mine on networks 2 or 3, this can be changed by editing the file /etc/init.d/p2pool-n in a text
editor like nano or gedit, and changing the DAEMON_ARGS line, replacing the section "--net vertcoin" with 
--net vertcoin2 (for network 2 on port 9172) or --net vertcoin3 (for network 3 on port 9174).

Note if adding any further services (such as additional merged coins) to the image, you may wish to update
the /etc/iptables.sav file which contains iptables firewall rules, and either reboot or run 
"sudo iptables-restore </etc/iptables.sav" to reload the rule set. The default ruleset allows incoming
connections only for p2pool mining and blockchain sync.

The QT wallets are started on boot using the normal Ubuntu "startup applications" dialog in system settings.
The links on the desktop are simply softlinks to the binaries in /usr/local/bin

The Vertcoin daemon is also in /usr/local/bin and is started at boot by the /etc/init.d/vertcoind script.

P2pool is in /home/p2pool/p2pool-n and is run on boot by the /etc/init.d/p2pool script

NOTE: Do not give a copy of your image to anyone else unless you first remove the wallet.dat files from
the .vertcoin, .monocle and .parallaxcoin directories in /home/p2pool/ otherwise you will be giving away
your secret keys! The image comes with those pre-deleted, and fresh wallets are created automatically the
first time you boot the image.


