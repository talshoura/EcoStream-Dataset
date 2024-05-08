
<a id="idtext"></a> 
#  Resource Utilization Dataset
Please feel free to [**view and download the dataset here**](https://drive.google.com/drive/folders/1EM_fpRZug_8PZEgvTQa-mqhfO0gYtv9y?usp=sharing) \
For comments, feedback, or question please [Contact Us](mailto:Tariq.AlShoura@ucalgary.ca?subject=Inquiry%20about%20the%20Resource%20Utilization%20Dataset) at: [Tariq.AlShoura@ucalgary.ca](mailto:Tariq.AlShoura@ucalgary.ca?subject=Inquiry%20about%20the%20Resource%20Utilization%20Dataset)

<!-- Please cite the following paper if using the dataset:
> Tariq Al Shoura, Ali Mollaahmadi Dehaghi, Reza Razavi, Behrouz Far, and Mohammad Moshirpour. 2023. SEPE Dataset: 8K Video Sequences and Images for Analysis and Development. In Proceedings of the 14th ACM Multimedia Systems Conference (MMSys ’23), June 7–10, 2023, Vancouver, BC, Canada.  -->

## Table of Contents
- [Utilization Data Collection](#utilization-data-collection)
  * [Video File and Stream Data](#video-file-and-stream-data)
  * [CPU Utilization and Power Data](#cpu-utilization-and-power-data)
  * [Memory Utilization Data](#memory-utilization-data)
  * [GPU Utilization and Power Data](#gpu-utilization-and-power-data)
  * [Network Data](#network-data)
- [Data Samples](#data-samples)
  * [Video File and Stream Data Sample](#video-file-and-stream-data-sample)
  * [CPU Data Sample](#cpu-data-sample)
  * [Memory Data Sample](#memory-data-sample)
  * [GPU Data Sample](#gpu-data-sample)
  * [Network Data Sample](#network-data-sample)
- [Training Data Logs](#training-data-logs)
___








<br>
<br>
<br>
<br>

# Utilization Data Collection
The basic commands' structure used to collect the data have been provided in the paper. This section presents samples of the commands used.



## Video File and Stream Data
The data related to the video is gathered using ffprobe using the command:
``` bash
ffprobe -v error \
    -show_entries stream=avg_frame_rate,height,width,duration,nb_frames,codec_name,codec_type,pix_fmt \
    -show_entries format=filename,format_name,size,bit_rate \
    -of json=c=1 ${video_file_name}
```

The information is then parsed and stored in addition to the streaming parameter and the number of displays are then stored in  ```01-file_stream_data.csv```, a sample of output can be seen in [**<ins>here</ins>**](#video-file-and-stream-data-sample)
<br>

[Back to Top](#idtext)
___



## CPU Utilization and Power Data
The following is the baseline command to get the data, it should be noted that the timelines are recorded using linux functionalities such as `ts`, and the data is parsed and processed using `awk`.
``` bash
powerstat -n -f -t -D 1 ${streamCaptureDuration} >> ${outFile}.txt
```
*__Table  1__. CPU Variables Example*
<table>
  <tr>
    <th>Variable</th>
    <th>Value</th>
  </tr>
  <tr>
    <td><code>${streamCaptureDuration}</code></td>
    <td>35</td>
  </tr>
  <tr>
    <td><code>${outFile}</code></td>
    <td>02-cpu_pow.csv</td>
  </tr>
</table>

Sample of the outputs can be seen [**<ins>here</ins>**](#cpu-data-sample)
<br>

[Back to Top](#idtext)
___



## Memory Utilization Data
The following is the baseline command to get the data, it should be noted that the timelines are recorded using linux functionalities such as `ts`, and the data is parsed and processed using `awk`.
``` bash
free -s 1 -c ${streamCaptureDuration} > ${outFile}.txt
```
*__Table  2__. CPU Variables Example*
<table>
  <tr>
    <th>Variable</th>
    <th>Value</th>
  </tr>
  <tr>
    <td><code>${streamCaptureDuration}</code></td>
    <td>35</td>
  </tr>
  <tr>
    <td><code>${outFile}</code></td>
    <td>03-mem.csv</td>
  </tr>
</table>

Sample of the outputs can be seen [**<ins>here</ins>**](#memory-data-sample)
<br>

[Back to Top](#idtext)
___



## GPU Utilization and Power Data
The following is the baseline command to get the data, it should be noted that the timelines are recorded using linux functionalities such as `ts`, and the data is parsed and processed using `awk`.
``` bash
nvidia-smi dmon -i 0 -s uctpvme -c ${streamCaptureDuration} > ${outFile}.txt
```
*__Table  3__. GPU Variables Example*
<table>
  <tr>
    <th>Variable</th>
    <th>Value</th>
  </tr>
  <tr>
    <td><code>${streamCaptureDuration}</code></td>
    <td>35</td>
  </tr>
  <tr>
    <td><code>${outFile}</code></td>
    <td>04-gpu.csv</td>
  </tr>
</table>

Sample of the outputs can be seen [**<ins>here</ins>**](#gpu-data-sample)
<br>

[Back to Top](#idtext)
___



## Network Data
The following command is used to start tshark/wireshark to monitor the network traffic
``` bash
tshark -i ${portID} -a duration:${streamCaptureDuration} -w ${outFile}.pcap
```
*__Table  4__. GPU Variables Example*
<table>
  <tr>
    <th>Variable</th>
    <th>Value</th>
  </tr>
  <tr>
    <td><code>${streamCaptureDuration}</code></td>
    <td>35</td>
  </tr>
  <tr>
    <td><code>${outFile}</code></td>
    <td>04-gpu.csv</td>
  </tr>
</table>

The raw ```.pcap``` files are available [**<ins>here</ins>**](https://drive.google.com/drive/folders/1S4K7TSWXe20ec8mG81PWhZyIwHzH-vCz?usp=sharing)

The ```.pcap``` files are then processed using the following command
``` bash
tshark -r ${fileName} -d tcp.port==5554,rtsp -qz io, stat,1,FRAMES,BYTES, \
"FRAMES()ip.src == 192.168.168.97", \
"BYTES()ip.src == 192.168.168.97", \
"FRAMES()ip.dst == 192.168.168.97", \
"BYTES()ip.dst == 192.168.168.97", \
"MIN(tcp.analysis.initial_rtt)tcp.analysis.initial_rtt and ip.addr==192.168.168.97", \
"MAX(tcp.analysis.initial_rtt)tcp.analysis.initial_rtt and ip.addr==192.168.168.97", \
"AVG(tcp.analysis.initial_rtt)tcp.analysis.initial_rtt and ip.addr==192.168.168.97", \
"MIN(tcp.analysis.ack_rtt)tcp.analysis.ack_rtt and ip.addr==192.168.168.97", \
"MAX(tcp.analysis.ack_rtt)tcp.analysis.ack_rtt and ip.addr==192.168.168.97", \
"AVG(tcp.analysis.ack_rtt)tcp.analysis.ack_rtt and ip.addr==192.168.168.97", \
"COUNT(rtp.payload)rtp.payload"
```

Sample of the outputs can be seen [**<ins>here</ins>**](#network-data-sample)
<br>

[Back to Top](#idtext)
___








<br>
<br>
<br>
<br>

# Data Samples
This section presents samples of the data collected



## Video File and Stream Data Sample

*__Table  5__. 01-file_stream_data.csv data samples*
| **.streams.0.codec_name** | **.streams.0.codec_type** | **.streams.0.width** | **.streams.0.height** | **.streams.0.pix_fmt** | **.streams.0.avg_frame_rate** | **.streams.0.duration** | **.streams.0.nb_frames** | **.format.filename** | **.format.format_name** | **.format.size** | **.format.bit_rate** | **.mode** | **.parameter_val** | **.out_codec** | **.screens_w** | **.screens_h** |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| hevc | video | 1920 | 1080 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/001_hevc_1K_qp_13.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 16330847 | 13051625 | fixed_QP | 20 | hevc | 1 | 1 |
| hevc | video | 1920 | 1080 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/001_hevc_1K_qp_13.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 16330847 | 13051625 | fixed_QP | 22 | hevc | 1 | 1 |
| hevc | video | 1920 | 1080 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/001_hevc_1K_qp_13.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 16330847 | 13051625 | fixed_QP | 25 | hevc | 1 | 1 |
| hevc | video | 854 | 480 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/011_hevc_480p_qp_47.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 47504 | 37965 | fixed_QP | 35 | hevc | 2 | 2 |
| hevc | video | 854 | 480 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/011_hevc_480p_qp_47.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 47504 | 37965 | fixed_QP | 40 | hevc | 2 | 2 |
| hevc | video | 3840 | 2160 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/011_hevc_4K_qp_13.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 24026087 | 19201667 | fixed_QP | 20 | hevc | 2 | 2 |
| hevc | video | 1280 | 720 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/003_hevc_720p_qp_23.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 1577244 | 1260534 | fixed_QP | 40 | hevc | 3 | 3 |
| hevc | video | 1280 | 720 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/003_hevc_720p_qp_25.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 1216640 | 972339 | fixed_QP | 20 | hevc | 3 | 3 |
| hevc | video | 1280 | 720 | yuv420p | 30000/1001 | 10.010000 | 300 | ./media/003_hevc_720p_qp_25.mp4 | "mov,mp4,m4a,3gp,3g2,mj2" | 1216640 | 972339 | fixed_QP | 22 | hevc | 3 | 3 |

[Back to Top](#idtext)
___



## CPU Data Sample

*__Table  6__. 02-cpu_pow.csv data samples*
| **Time** | **Name** | **User** | **Nice** | **Sys** | **Idle** | **IO** | **Run** | **Ctxt/s** | **IRQ/s** | **Fork** | **Exec** | **Exit** | **Watts** | **pkg-0** | **core** | **acpitz** | **CPU Freq** | **Freq Min** | **Freq Max** |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1703385944.045753 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0.0 | 0.0 | 0.1 | 99.9 | 0.0 | 2 | 1043 | 746 | 0 | 0 | 0 | 2.26 | 2.21 | 0.05 | 38.00 | 1.40 GHz | 1.37 GHz | 1.45 GHz |
| 1703385945.044905 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 1.6 | 0.0 | 1.4 | 97.0 | 0.0 | 3 | 8588 | 4715 | 82 | 61 | 55 | 8.29 | 7.94 | 0.34 | 38.00 | 2.19 GHz | 1.61 GHz | 4.46 GHz |
| 1703385946.045151 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0.3 | 0.0 | 0.2 | 99.6 | 0.0 | 1 | 3023 | 2061 | 0 | 0 | 0 | 4.09 | 3.89 | 0.20 | 38.00 | 1.40 GHz | 1.32 GHz | 1.55 GHz |
| 1703385947.045294 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0.4 | 0.0 | 0.1 | 99.5 | 0.0 | 2 | 3180 | 2137 | 0 | 0 | 0 | 3.85 | 3.73 | 0.12 | 38.00 | 1.42 GHz | 1.36 GHz | 1.71 GHz |
| 1703844970.615229 | 004_hevc_2K_qp_23-fixed_QP_27_hevc_3_3 | 10.5 | 0.0 | 0.4 | 89.1 | 0.0 | 2 | 11829 | 8619 | 0 | 0 | 0 | 17.38 | 16.28 | 1.11 | 45.00 | 2.11 GHz | 2.01 GHz | 2.27 GHz |
| 1703844971.615249 | 004_hevc_2K_qp_23-fixed_QP_27_hevc_3_3 | 11.5 | 0.0 | 0.4 | 88.2 | 0.0 | 1 | 11686 | 8383 | 0 | 0 | 0 | 18.07 | 17.10 | 0.97 | 45.00 | 2.15 GHz | 2.00 GHz | 2.69 GHz |
| 1703844972.615197 | 004_hevc_2K_qp_23-fixed_QP_27_hevc_3_3 | 8.6 | 0.0 | 0.8 | 90.5 | 0.0 | 2 | 12648 | 9344 | 0 | 0 | 44 | 17.93 | 16.81 | 1.12 | 45.00 | 2.22 GHz | 1.92 GHz | 2.90 GHz |
| 1703844973.615357 | 004_hevc_2K_qp_23-fixed_QP_27_hevc_3_3 | 0.3 | 0.0 | 1.7 | 98.0 | 0.0 | 2 | 4797 | 3554 | 0 | 0 | 22 | 7.95 | 7.83 | 0.13 | 45.00 | 1.94 GHz | 1.52 GHz | 4.37 GHz |

[Back to Top](#idtext)
___



## Memory Data Sample

*__Table  7__. 03-mem.csv data samples*
| **time** | **name** | **memory_total** | **memory_used** | **memory_free** | **memory_shared** | **memory_buff** | **memory_available** | **swap_total** | **swap_used** | **swap_free** |
|---|---|---|---|---|---|---|---|---|---|---|
| 1703385944.569552 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 32069712 | 1571688 | 2790076 | 4952 | 27707948 | 30028836 | 2097148 | 1024 | 2096124 |
| 1703385945.569799 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 32069712 | 1890868 | 2405868 | 69984 | 27772976 | 29644632 | 2097148 | 1024 | 2096124 |
| 1703385946.570014 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 32069712 | 1891120 | 2405616 | 69984 | 27772976 | 29644380 | 2097148 | 1024 | 2096124 |
| 1703385947.571048 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 32069712 | 1891120 | 2405616 | 69984 | 27772976 | 29644380 | 2097148 | 1024 | 2096124 |
| 1707828814.933350 | 028_hevc_4K_qp_27-fixed_QP_40_hevc_2_2 | 32069716 | 2957552 | 277036 | 238104 | 28835128 | 28409832 | 2097148 | 3072 | 2094076 |
| 1707828815.933536 | 028_hevc_4K_qp_27-fixed_QP_40_hevc_2_2 | 32069716 | 2957796 | 276784 | 238104 | 28835136 | 28409580 | 2097148 | 3072 | 2094076 |
| 1707828816.934129 | 028_hevc_4K_qp_27-fixed_QP_40_hevc_2_2 | 32069716 | 2957796 | 276784 | 238104 | 28835136 | 28409580 | 2097148 | 3072 | 2094076 |
| 1707828817.934804 | 028_hevc_4K_qp_27-fixed_QP_40_hevc_2_2 | 32069716 | 1977860 | 1492260 | 2576 | 28599596 | 29625048 | 2097148 | 3072 | 2094076 |

[Back to Top](#idtext)
___



## GPU Data Sample

*__Table  8__. 04-gpu.csv data samples*
| **time** | **name** | **gpu(Idx)** | **sm(%)** | **mem(%)** | **enc(%)** | **dec(%)** | **jpg(%)** | **ofa(%)** | **mclk(MHz)** | **pclk(MHz)** | **rxpci(MB/s)** | **txpci(MB/s)** | **power(W)** | **gtemp(C)** | **mtemp(C)** | **pviol(%)** | **tviol(bool)** | **fb(MB)** | **bar1(MB)** | **ccpm(MB)** | **sbecc(errors)** | **dbecc(errors)** | **pci(error)** |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1703385944.867071 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 7000 | 1702 | 0 | 0 | 23 | 43 | - | 0 | 0 | 9 | 3 | 0 | - | - | 0 |
| 1703385945.937894 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0 | 2 | 1 | 12 | 2 | 0 | 0 | 7000 | 1702 | 167 | 180 | 24 | 43 | - | 0 | 0 | 465 | 7 | 0 | - | - | 0 |
| 1703385946.994135 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0 | 2 | 1 | 11 | 2 | 0 | 0 | 7000 | 1702 | 168 | 0 | 24 | 43 | - | 0 | 0 | 465 | 7 | 0 | - | - | 0 |
| 1703385948.065366 | 001_hevc_1K_qp_32-fixed_QP_30_hevc_1_1 | 0 | 2 | 1 | 6 | 2 | 0 | 0 | 7000 | 1702 | 168 | 180 | 24 | 43 | - | 0 | 0 | 465 | 7 | 0 | - | - | 0 |
| 1703867408.077800 | 005_hevc_4K_qp_32-fixed_QP_20_hevc_3_3 | 0 | 11 | 4 | 61 | 6 | 0 | 0 | 7000 | 1987 | 1503 | 3 | 40 | 57 | - | 0 | 0 | 2462 | 24 | 0 | - | - | 0 |
| 1703867409.128044 | 005_hevc_4K_qp_32-fixed_QP_20_hevc_3_3 | 0 | 11 | 5 | 75 | 6 | 0 | 0 | 7000 | 1987 | 1499 | 738 | 40 | 58 | - | 0 | 0 | 2462 | 24 | 0 | - | - | 0 |
| 1703867410.303194 | 005_hevc_4K_qp_32-fixed_QP_20_hevc_3_3 | 0 | 16 | 3 | 0 | 0 | 0 | 0 | 7000 | 1987 | 0 | 0 | 37 | 57 | - | 0 | 0 | 9 | 3 | 0 | - | - | 0 |
| 1703867411.418285 | 005_hevc_4K_qp_32-fixed_QP_20_hevc_3_3 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 7000 | 1987 | 0 | 0 | 32 | 57 | - | 0 | 0 | 9 | 3 | 0 | - | - | 0 |

[Back to Top](#idtext)
___



## Network Data Sample

*__Table  9__. 05-tshark_data.csv data samples*
| **FNAME** | **SECONDS** | **FRAMES** | **BYTES** | **FRAMES_from_SrcIP** | **BYTES_from_SrcIP** | **FRAMES_to_DstIP** | **BYTES_to_DstIP** | **MIN_initial_rtt_from_SrcIP** | **MAX_initial_rtt_from_SrcIP** | **AVG_initial_rtt_from_SrcIP** | **MIN_ack_rtt_from_SrcIP** | **MAX_ack_rtt_from_SrcIP** | **AVG_ack_rtt_from_SrcIP** | **COUNT_rtp_payload** |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 001_hevc_1K_qp_13-fixed_QP_20_hevc_1_1.pcap | 10 | 0 | 0 | 0 | 0 | 0 | 0 | 0.000000 | 0.000000 | 0.000000 | 0.000000 | 0.000000 | 0.000000 | 0 |
| 001_hevc_1K_qp_13-fixed_QP_20_hevc_1_1.pcap | 11 | 224 | 206847 | 127 | 195250 | 69 | 5276 | 0.001185 | 0.001185 | 0.001185 | 0.000003 | 0.040164 | 0.000654 | 147 |
| 001_hevc_1K_qp_13-fixed_QP_20_hevc_1_1.pcap | 12 | 481 | 478684 | 349 | 469924 | 132 | 8760 | 0.001185 | 0.001185 | 0.001185 | 0.000005 | 0.020939 | 0.000448 | 345 |
| 001_hevc_1K_qp_13-fixed_QP_20_hevc_1_1.pcap | 13 | 287 | 255308 | 203 | 249764 | 84 | 5544 | 0.001185 | 0.001185 | 0.001185 | 0.000004 | 0.000072 | 0.000027 | 203 |
| 011_hevc_4K_qp_47-fixed_QP_27_hevc_2_2.pcap | 23 | 402 | 194948 | 230 | 183383 | 171 | 11286 | 0.000611 | 0.001259 | 0.000801 | 0.000006 | 0.000134 | 0.000035 | 230 |
| 011_hevc_4K_qp_47-fixed_QP_27_hevc_2_2.pcap | 24 | 281 | 95603 | 159 | 87330 | 121 | 7986 | 0.000611 | 0.001259 | 0.000806 | 0.000007 | 0.000094 | 0.000045 | 159 |
| 011_hevc_4K_qp_47-fixed_QP_27_hevc_2_2.pcap | 25 | 216 | 36594 | 135 | 30822 | 81 | 5772 | 0.000611 | 0.001259 | 0.000801 | 0.000020 | 0.040658 | 0.001515 | 124 |
| 011_hevc_4K_qp_47-fixed_QP_27_hevc_2_2.pcap | 26 | 4 | 378 | 2 | 108 | 2 | 270 | 0.000611 | 0.000611 | 0.000611 | 0.000000 | 0.000000 | 0.000000 | 0 |


[Back to Top](#idtext)
___








<br>
<br>
<br>
<br>

# Training Data Logs
This section presents the logs generated when training the models for each data point, these logs can be accessed [**<ins>here</ins>**](https://drive.google.com/drive/folders/14Wenrjd0a7oiQ9YNCbdcYUQ_RsE4JM0I?usp=sharing).


The files are devided for each output parameters wjere the it reflects 


The files are divided for each output parameters where each file reflects the training for the given output parameter.
The files are divided as follows:
-	Training linear models, where 10 different models would be trained and evaluated. The best model’s parameters will be printed out.
-	support vector regression models, where 20 different models would be trained and evaluated. The best model’s parameters will be printed out.
-	Gaussian process regression models, where 50 different models would be trained and evaluated. The best model’s parameters will be printed out.
-	Regression models, where 20 different models would be trained and evaluated. The best model’s parameters will be printed out.
-	Neural networks regression models, where 50 different models would be trained and evaluated. The best model’s parameters will be printed out.
-	Kernel functions regression models, where 20 different models would be trained and evaluated. The best model’s parameters will be printed out.
-	A summary of the best model performance from each category against the testing data.

