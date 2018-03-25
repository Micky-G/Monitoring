# Monitoring


**Monitoring Raspberry Pi stats:**

Download "telegraf_pi_temp.sh" and 'chmod +x' the script. Then call it from within telegraf using "[[inputs.exec]]" (already included telegraf.conf in this repo).

**Monitoring PiHole:**

 1. Download piholestats.py in this repo to your local machine 
 2. make  piholestats.py executable by "chmod +x" .
 3. Add the following to your telegraf.conf inputs

    [[inputs.exec]]
        commands = ["/bin/piholestats.py"]
        timeout = "10s"
        data_format = "json"
        name_suffix = "_pihole"

 4. Restart telegraf

**Synology SNMP:**


Make sure snmp-mibs-downloader is already installed. It will download and install additional MIBs during install.

    apt-get install snmp-mibs-downloader
then

    download-mibs

1.  edit /etc/snmp/snmp.conf and comment out the 'mibs:' line. Here is what mine looks like:
    
    ```
      GNU nano 2.7.4                                                                                                                   
    File: /etc/snmp/snmp.conf
    # As the snmp packages come without MIB files due to license reasons, loading
    # of MIBs is disabled by default. If you added the MIBs you can reenable
    # loading them by commenting out the following line.
    # mibs :
    
    ```
    
2.  Now when you do a SNMPwalk, it will automatically translate OIDs to Names. This is what it looks like against my Synology:
    
    ```
    kumar@raspberrypi:~ $ snmpwalk -c public -v 2c 192.168.1.5
    SNMPv2-MIB::sysDescr.0 = STRING: Linux DiskStation 3.10.102 SMP Fri Jan 26 06:46:44 CST 2018 x86_64
    SNMPv2-MIB::sysObjectID.0 = OID: NET-SNMP-MIB::netSnmpAgentOIDs.10
    DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (90908998) 10 days, 12:31:29.98
    SNMPv2-MIB::sysContact.0 = STRING: Redacted
    SNMPv2-MIB::sysName.0 = STRING: Synology DS416Play
    SNMPv2-MIB::sysLocation.0 = STRING: Home
    
    ```
    
3.  Obtain your MIBs (from here for example  [http://www.oidview.com/mibs/12356/FORTINET-FORTIGATE-MIB.html](http://www.oidview.com/mibs/12356/FORTINET-FORTIGATE-MIB.html)) and drop them in either of the below locations:

    /usr/share/snmp/mibs
    
    /home/$USER/.snmp/mibs

    
4.  Go through the linked page above or the snmpwalk output and make a list of OIDs you want to monitor (grep is your friend here)
    
5.  Add them to telegraf.conf using examples others have provided elsewhere in this thread
    
6.  Restart telegraf and test with the '-test' flag. To verify everything is working as expected.


**VMWare Monitoring**

https://github.com/Oxalide/vsphere-influxdb-go
