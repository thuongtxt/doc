

## Revision History 

| Revision | Date         | Author          | Description     |
| -------- | ----         | ----            | ----            |
|    1.0   | Dec 12, 2019 | Thuong Tran     | Initial version |


## Test Setup
* The device under test (DUT) consists of a system with an Intel® architecture motherboard populated with the following:
    * A single or dual processor and PCH chip, except for System on Chip (SoC) cases
    * DRAM memory size and frequency (normally single DIMM per channel)
    * Specific Intel Network Interface Cards (NICs)
    * BIOS settings noting those that updated from the basic settings
    * DPDK build configuration settings, and commands used for tests
* Connected to the DUT is an STC (Sprient Test Center), a hardware test and simulation platform to generate packet traffic to the DUT ports and determine the throughput at the tester side. The STC is used to implement RFC2544 on the DUT.

* DPDK Testpmd Test Case: Documentation may be found at
http://www.dpdk.org/doc/guides/testpmd_app_ug/index.html.
* The testpmd application can be used to test the DPDK in a packet forwarding mode and also to access
NIC hardware features. Note in the Testpmd example if the –i argument is used, the first core is used for
the command language interface (CLI).
## Test Model

![Model Test](images/test-model.png)

## Test Environment

### Hardware & Software Ingredients

|   Item    |   Description     ||
| ----------|-----------------  |
|   Server Platform     |   Asus Z10PE-D8 WS       |
|   CPU                 |   Intel(R) Xeon(R) CPU E5-2678 v3 @ 2.50GHz <br> https://ark.intel.com/content/www/us/en/ark/products/81908/intel-xeon-processor-e5-2680-v3-30m-cache-2-50-ghz.html <br> Number of cores: 48, Number of Threads:    |
|   Memory              |   Total  256GiB RIMM DDR4 Synchronous over 8 channels 2400 MHz  (0.4 ns)  |
|   PCIe                |   |
|   NICs                |   Intel (R) Ethernet Controller X710 for 10GbE SFP+ (4 x 10GbE) <br> Chelsio Communications Inc T62100-CR Unified Wire Ethernet Controller (5 x 40/50/100Gb Ethernet) <br> Intel (R) I210 Gigabit Network 4.15.0-63-genericrk Connection (2 x 1GbE) |
|   Operating System    |   Ubuntu 18.04.3 LTS  |
|   BIOS                |   12/18/2015 American Megatrends Inc. 3204    |
|   Microcode           |   0x43    |
|  Linux kernel version |   4.15.0-63-generic   |
|   Gcc version         |   gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0   |
|   DPDK version        |   18.11.5 |

### Boot and BIOS settings
|   Item                |   Description     ||
|--------------------   |-------------------|
|   BIOS Setting        |   <i>default_hugepagesz=1G hugepagesz=1G hugepages=16 intel_iommu=on iommu=pt isolcpus=1-21,28-48 nohz_full=1-21,28-48 rcu_nocbs=1- 21,28-48</i> <br> <b><u>Note:</u></b> nohz_full and rcu_nocbs is to disable Linux* kernel interrupts, and it's important for zero-packet loss test. Generally, 1G huge pages are used for performance test.   |
|   BIOS                |   CPU Power and Performance Policy (Performance) <br> CPU C-state Disabled <br> CPU P-state Disabled <br> Enhanced Intel (R) Speedstep (R) Tech Disabled <br> Turbo Boost Disabled<br> Intel VT Fordirected I/O(VT-d) Enable<br> Intel Virtualization Technology (VT-x) Enable   |
|  DPDK Setting         |   Build Testpmd   |

|   Item                |   Description     ||
|---------------------  |   ------------    |
|   Test case           |   RFC2544 zero packet loss test on Intel (R) Ethernet Controller X710 for 10GbE (1GbE x 2)     |
|   NIC                 |   Intel(R) Ethernet Controller X710 for 10GbE     |
|   Driver              |   i40e DPDK PMD (base on igb_uio)    |
|   Device ID           |     8086:1572      |
|   Device Driver/<br> Firmware 	|     Driver version: 2.9.21 <br> firmware-version: 7.10  xxx 11.2019  |
|   Test configuration  |   	|
|   Command line        |   <i>sudo ./build/app/testpmd --file-prefix "thuongtxt" -w 0000:04:00.0 -w 0000:04:00.1 -l 12,13,14 -n 4  -- -i</i>

## Test Result

|   Packet Size(Bytes)  |   Throughput(Mpps)    |   Line rate%   ||
|   -----------------   |   ----------------    |   ----------   |
|   64                  |   14.85               |   |
|   128                 |   8.41                |   |
|   256                 |   4.51                |   |
|   512                 |   2.34                |   |


## Figure3: RFC2544 zero packet loss test on Intel® Ethernet Converged Network Adapter X710-DA4 
!["Chart"](images/chart.png)
## References
<i>[1] https://fast.dpdk.org/doc/perf/DPDK_19_08_Intel_NIC_performance_report.pdf</i>

## About Arrive 

Arrive is a broadband semiconductor solutions company with a broad portfolio of
highly integrated systems-on-a-chip products combining voice, data, Internet and
multimedia content for worldwide telecommunications companies.

Our CodeChip (TM) replaces inflexible fixed-silicon ASICs with a programmable
carrier-class FPGA solution.  It includes a SoC FPGA Image with a full software
development kit, including APIs and Drivers, and is backed by the integration
and testing experience of Arrive.

For more information on our Carrier Ethernet CodeChip� product, go to:
[www.arrivetechnologies.com/af5/](http://www.arrivetechnologies.com/af5/)

<table class="columns" style="font-size:1em;margin-bottom:10px;margin-top:30px"> <tr> <td>

<b>North America Headquarters</b><br>
4031 White Mill Crescent Road<br>
Roseville, CA 95747<br>
USA<br>
(888) 864-6959

</td> <td style="text-align:right"> 

<b>Vietnam</b><br>
Floor 10, E-Town Building,<br>
364 Cong Hoa Street<br>
Ward 13, Tan Binh District,<br>
Ho Chi Minh City, Vietnam

</td> </tr> </table>

<!-- End of document -->
