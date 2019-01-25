Source code of the [DCT2Gen traffic generator] (http://dl.acm.org/citation.cfm?id=2906504)

This document is copied from Paderborn University webpage ( https://cs.uni-paderborn.de/?id=65279)

DCT2Gen takes a description of certain properties of observed Layer 2 traffic and generates a schedule of TCP payload transmissions. When this schedule is executed, this generates Layer 2 traffic on the network having the same properties as the observed Layer 2 traffic.

A detailed description of DCT2Gen can be found in our paper Philip Wette and Holger Karl: DCT2Gen: A Versatile TCP Traffic Generator for Data Centers, 2014.
Here you can find a print and a pre-print on arXiv.

Download DCT2Gen ver. 0.02

-> GitHub

DCT2Gen comes with a set of csv-files describing the six properties

    Flow sizes (S_obs)
    Flow inter-arrival times (IAT_obs)
    Number of communication partners per node (N_obs_inter, N_obs_intra)
    Number of bytes per traffic Matrix entry (B_obs_inter, B_obs_intra)

and with the inferred properties

    B_PL_inter
    B_PL_intra

To run DCT2Gen, you need a running installation of GNU R, Java, and Gurobi (with a valid license).
If Gurobis Java bindings are not installed to <tt>/opt/gurobi562/linux64/lib/gurobi.jar</tt> you have to change that path accordingly in DCT2Gens source code.

To compute a traffic schedule, use the function <tt>makeTraffic(numMatrices, numRacks, hostsPerRack, trafficScale, fileName)</tt>
<tt>numMatrices</tt> is the number of traffic matrices to generate. Each traffic matrix stands for 10s of traffic.
<tt>numRacks</tt> is the number of racks in the data center
<tt>hostsPerRack</tt> is the number of hosts in one rack
<tt>trafficScale</tt> is a factor to linearly scale the size of the traffic matrices.
<tt>fileName</tt> is the filename storing the generated traffic schedule.

<tt>makeTraffic(..)</tt> uses the files <tt>S_obs.csv, IAT_obs.csv, N_obs_inter.csv, N_obs_intra.csv, B_TM_inter.csv, B_TM_intra.csv</tt>.
B_TM_inter, B_TM_intra are inferred from B_obs_inter, B_obs_intra. Everytime you update B_obs_inter, B_obs_intra you have to use the functions <tt>B_obs_inter2B_PL_inter()</tt> and <tt>B_obs_intra2B_PL_intra()</tt> (from <tt>Deconvolver.R</tt>) to recreate B_TM_inter and B_TM_intra.
But before you use these functions, please make sure you understand what the Deconvolver does. Be aware that the deconvolving routines take a lot of CPU time.

DCT2Gen creates a schedule of TCP payload transmissions between all hosts in a data center.
The schedule is a csv file where each line <tt>srcHost, dstHost, startTime, payloadBytes</tt> represents one backlogged TCP connection between srcHost and dstHost that is established at startTime and transfers payloadBytes Bytes of payload from srcHost to dstHost.

Host IDs are created based on their location in the datacenter. They start at <tt>1</tt> and end at <tt>numRacks * hostsPerRack</tt>. Hosts <tt>1</tt> to <tt>hostsPerRack</tt> are located in rack <tt>1</tt>, Hosts <tt>hostsPerRack + 1</tt> to <tt>2 * hostsPerRack</tt> in rack <tt>2</tt>, and so on.
