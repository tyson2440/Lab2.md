<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/d41556e0-0b35-49c8-8c51-6dbc17d6083c" />## 3. Task 1 — Confirm the Traffic Direction

Open each JSON file and find:

```text
start → test_start → reverse
```

- `reverse: 1` means downlink in this lab.
- `reverse: 0` means uplink in this lab.

Complete the table:

| Scenario | Reverse value | Direction |
| --- | ---: | --- |
| A | 1 | downlink |
| B | 0 | uplink |
| C | 0 | uplink |

**Question:** Does the traffic direction shown in the JSON match the scenario description? Show the proof.

The JSON traffic direction can be identified from the reverse parameter. To confirm if it matches each scenario description, we must compare these values with the expected traffic direction specified for A, B, and C.

A)<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/4fd886af-1a15-452d-97f7-0b82942518bd" />
B)<img width="1916" height="1020" alt="image" src="https://github.com/user-attachments/assets/52e9a5fa-0093-40a5-9d62-87dc169ce496" />
C)<img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/f0b27857-3731-4c93-85d3-a3accde3cee1" />

## 4. Task 2 — Compare Throughput

In each JSON file, find:

```text
end → sum → bits_per_second
```

Convert the value to Mbps:

```text
Throughput (Mbps) = bits_per_second ÷ 1,000,000
```

Complete the table:

| Scenario | Direction | Throughput (Mbps) |
| --- | --- | ---: |
| A | Downlink | 90.654 |
| B | Uplink | 109.117 |
| C | Uplink | 124.416 |

Calculate how much C improves over B: 

```text
Improvement (%) = (124.416 - 109.117) ÷ 109.117 × 100
```

Answer:

1. What is the downlink throughput in Scenario A?

   The downlink  throughput in Scenario A is 90.654 Mbps

2. What is the uplink throughput in Scenario B?

   The uplink throughput in Scenario B is 109.117 Mbps

3. Is the uplink throughput higher in B or C?

   The uplink throughput is higher in Scenario C, with 124.416 Mbps compared to 109.117 Mbps in Scenario B.

4. By approximately what percentage does C improve over B?

   By 14.02%

> Use the JSON `bits_per_second` value. Do not recalculate throughput using the other time fields.

## 5. Task 3 — Check the Trade-off

In each JSON file, find:

```text
end → sum → lost_percent
end → sum → jitter_ms
```

Complete the table:

| Scenario | Throughput (Mbps) | Packet loss (%) | Jitter (ms) |
| --- | ---: | ---: | ---: |
| B | 109.117 | 0.2357 | 0.1162 |
| C | 124.416 | 2.1339 | 0.1620 |

Answer:

1. Does C have higher uplink throughput than B?

   Compared with Scenario B (109.1 Mbps), Scenario C (124.4 Mbps) has higher throughput 

2. Does C have lower packet loss than B?

   Scenario C (2.13%) has higher packet loss than Scenario B (0.23%)  

3. What trade-off, if any, is visible when changing from B to C?

   Scenario C provides higher throughput, but it also has higher packet loss and jitter. So increasing uplink throughput comes at the cost of reduced transmission reliability     and          stability.

A simple answer format is:

> Compared with Scenario B, Scenario C has ______ throughput, ______ packet loss, and ______ jitter. These results suggest ______.

Use the measured values. If one result improves while another worsens, explain that trade-off. Do not assume that increasing UL resources always increases packet loss.

## 6. Task 4 — Read SIB1 and Draw the TDD Configuration

Use these **two configuration captures** for this task:

- `tdd-dl-heavy-sib1.pcapng`: Reference configuration for Scenarios A and B.
- `tdd-balanced-sib1.pcapng`: Reference configuration for Scenario C.

These files were recorded separately from the original iperf3 tests. Use them to examine the supplied reference settings.

### 4.1 What Is SIB1?

A gNB broadcasts system information so that a UE can learn how to access the cell.

- **MIB (Master Information Block):** Provides basic information, including parameters needed to find SIB1.
- **SIB1 (System Information Block Type 1):** Provides cell-access and common radio configuration information. In this TDD lab, it includes `tdd-UL-DL-ConfigurationCommon`.

You will read the **common TDD configuration in SIB1**, not decode every MIB/SIB field. This configuration describes DL, UL, and flexible time resources; it is not a record of the actual data scheduled in every slot.

### 4.2 Enable MAC-NR Decoding and Find SIB1

These captures contain NR MAC messages carried over UDP. **Enable MAC-NR-over-UDP decoding before applying the SIB1 filter.** Without this decoder, the packets may appear only as UDP and the SIB1 filter may return no results.

1. Open `tdd-dl-heavy-sib1.pcapng` in Wireshark.
2. Open **Analyze → Enabled Protocols**. Locate **MAC-NR over UDP** (`mac_nr_udp`) in the heuristic dissectors list and enable it. Depending on the Wireshark version, the heuristic list may be shown separately. Apply the change and reload the capture if needed.
3. Apply this display filter:

   ```text
   nr-rrc.systemInformationBlockType1_element
   ```

4. Select a SIB1 packet. In the packet details, expand:

   ```text
   NR Radio Resource Control (RRC) protocol
     → BCCH-DL-SCH-Message
       → message
         → c1
           → systemInformationBlockType1
             → servingCellConfigCommon
               → tdd-UL-DL-ConfigurationCommon
   ```

5. Read `referenceSubcarrierSpacing`, then expand `pattern1` and record:

   ```text
   dl-UL-TransmissionPeriodicity
   nrofDownlinkSlots
   nrofDownlinkSymbols
   nrofUplinkSlots
   nrofUplinkSymbols
   ```

6. Check whether `pattern2` is also present.
7. Repeat for `tdd-balanced-sib1.pcapng`.

Record the **decoded labels and units** shown in Wireshark. For example, record `30 kHz` or `5 ms`, not the enumeration number displayed in parentheses.

You can also locate packets containing the TDD configuration with:

```text
nr-rrc.tdd_UL_DL_ConfigurationCommon_element
```

Confirm that the selected TDD fields are inside **SIB1**. If a filter is not recognized in your version, try `nr-rrc` and locate `systemInformationBlockType1` manually.

> **Troubleshooting:** If packets still appear only as UDP, check that the **MAC-NR over UDP heuristic dissector** is enabled, rather than only the MAC-NR protocol. If the file still does not decode, ask the instructor to check the settings. Do not infer TDD parameters from packet timing. Graphical menu labels may vary by version.

### 4.3 Record the TDD Parameters

Complete the table for `pattern1`:

| Item | DL-heavy: A/B | Balanced: C |
| --- | --- | --- |
| Configuration capture filename | tdd-dl-heavy-sib1.pcapng | tdd-balanced-sib1.pcapng |
| SIB1 packet number | 2 | 2 |
| `referenceSubcarrierSpacing` (kHz) | 30 | 30 |
| `dl-UL-TransmissionPeriodicity` (ms) | 5 | 2.5 |
| `nrofDownlinkSlots` | 7 | 2 |
| `nrofDownlinkSymbols` | 6 | 6 |
| `nrofUplinkSlots` | 2 | 2 |
| `nrofUplinkSymbols` | 4 | 4 |
| Is `pattern2` present? | no | no |

DL- heavy) <img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/060ce419-ae10-4604-a6d7-0e9f76f6fe8b" />

Balanced) <img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/45665a6e-1354-41a6-b66e-01ff1de2a53e" />
 


If `pattern2` is present, add another table containing its period and four slot/symbol counts. It uses the same reference SCS.

Take **one screenshot per configuration** showing the selected SIB1 packet and the expanded TDD fields. Include `pattern2` in an additional screenshot if it is present and does not fit.

### 4.4 Draw the TDD Time Allocation

Draw one diagram for DL-heavy and one for balanced. **Use the same 0–5 ms time axis for both diagrams.** Derive each repeating period from SIB1 and repeat the shorter pattern as needed to fill the 5 ms interval. Mark every repeating-period boundary.

A spreadsheet, a drawing tool, or a hand-drawn diagram is acceptable. Include the diagrams as images in your report.

For the normal cyclic prefix used in this exercise, each reference slot contains **14 OFDM symbols**, numbered **0–13**. Use the SIB1 **reference SCS** to construct the common pattern:

| Reference SCS | Reference slot duration |
| --- | ---: |
| 15 kHz | 1 ms |
| 30 kHz | 0.5 ms |
| 60 kHz | 0.25 ms |
| 120 kHz | 0.125 ms |

```text
Number of reference slots in a pattern = Pattern period ÷ Reference slot duration
```

For each pattern:

1. Place `nrofDownlinkSlots` full DL slots at the beginning.
2. If `nrofDownlinkSymbols` is nonzero, mark that many symbols at the beginning of the next slot as DL.
3. Place `nrofUplinkSlots` full UL slots at the end.
4. If `nrofUplinkSymbols` is nonzero, mark that many symbols at the end of the slot immediately before the full UL slots as UL. If there are no full UL slots, use the last slot of the pattern.
5. Mark all remaining symbols as **F (flexible)**. The partial DL and partial UL portions may be in the same slot or in different slots.

Use **D = downlink**, **U = uplink**, and **F = flexible**. Include a legend, the common 0–5 ms time scale, slot indices, and repeating-period boundaries. You may restart the local slot index at zero at each period boundary, but label the boundaries clearly. Expand any mixed slot to show all 14 symbols. Do not automatically label flexible symbols as UL or guard time.

If `pattern2` is present, draw `pattern1` followed by `pattern2`; the combined sequence repeats. Label each pattern boundary and show the full combined duration.

First, count the symbols over **one complete repeating sequence** for each configuration. The two sequences may have different durations, so their raw UL symbol counts alone cannot be used to compare resources per unit time:

```text
Configured DL symbols = 14 × Full DL slots + Partial DL symbols
Configured UL symbols = 14 × Full UL slots + Partial UL symbols
Flexible symbols = Total symbols - Configured DL symbols - Configured UL symbols
Configured UL symbol share (%) = Configured UL symbols ÷ Total symbols × 100
```

For two patterns, sum the counts and durations of both patterns before calculating the share. These are symbol-count shares of the common configuration, not exact airtime measurements or measured throughput shares.
<img width="2365" height="1272" alt="balanced_tdd_allocation" src="https://github.com/user-attachments/assets/0a91c2d1-be20-417e-8184-cd046955180e" />
<img width="2365" height="1272" alt="dl_heavy_tdd_allocation" src="https://github.com/user-attachments/assets/4c59faf6-57d6-4416-8e12-58e485706a03" />

| TDD setting | Repeating duration (ms) | Total reference slots | DL symbols | UL symbols | Flexible symbols | Configured UL symbol share (%) |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| DL-heavy | 5 | 10 | 104 | 32 | 4| 22.9% |
| Balanced | 2.5 | 5 | 34 | 32 | 4 | 45.7% |

Next, count the symbols over the **same 5 ms interval** shown in your diagrams. Multiply each per-sequence count by the number of complete repetitions in 5 ms. For these captures, 5 ms contains a whole number of repetitions for both configurations.

| TDD setting | Repeating duration (ms) | Repetitions in 5 ms | DL symbols in 5 ms | UL symbols in 5 ms | Flexible symbols in 5 ms |
| --- | ---: | ---: | ---: | ---: | ---: |
| DL-heavy | 5 | 1 | 104 | 32 | 4 |
| Balanced | 2.5 | 2 | 68| 64 | 8 |

**Question:** Can two patterns have the same UL symbol count per period but provide different numbers of UL symbols per unit time? Explain using your two tables.
Yes, both configurations may have the same number of UL symbols in each pattern, but they do not provide the same uplink capacity over time because their repetition periods are different. The Balanced pattern repeats every 2.5 ms, while DL-heavy repeats every 5 ms, allowing Balanced to provide twice as many UL symbols within 5 ms (64 instead of 32). As a result, Balanced has a higher uplink symbol share of 45.7%, compared to 22.9% for DL-heavy.

### 4.5 Connect SIB1 to the Performance Results

Use the supplied mapping between the reference configuration captures and scenarios. Complete this table using Tasks 1–3 and your SIB1 analysis:

| Scenario | Reference TDD setting | JSON traffic direction | Configured UL symbol share (%) | Throughput (Mbps) | Packet loss (%) |
| --- | --- | --- | ---: | ---: | ---: |
| A | DL-heavy | Downlink | 22.86 | 90.654 | 0.4429 |
| B | DL-heavy | Uplink | 22.86 | 109.117 | 0.2356 |
| C | Balanced | Uplink | 45.71 | 124.416 |  2.1339 |

Answer:

1. Which decoded TDD parameters differ between DL-heavy and balanced?

The main differences are the TDD periodicity and the number of downlink slots. DL-heavy uses a 5 ms periodicity and 7 downlink slots, while Balanced uses a 2.5 ms periodicity and 2 downlink slots. Both configurations use 30 kHz subcarrier spacing, 2 uplink slots, 6   downlink symbols and 4 uplink symbols.

2. Which configuration provides more configured UL symbols per unit time? Support your answer with the diagram and calculated share.

The Balanced configuration provides more uplink symbols per unit time. Its configured UL symbol share is 45.71%, compared to 22.86% for DL-heavy. Therefore, Balanced provides twice the configured uplink symbol share.

3. How does that difference help explain the B/C throughput comparison?

Scenario C hs higher uplink throughput (124.416 Mbps) than Scenario B (109.117 Mbps), an improvement of approximately 14.02%. The higher configured UL symbol share in Scenario C provides more opportunities for uplink transmission, which helps explain the throughput improvement.

However, Scenario C also has higher packet loss (2.1339%) than Scenario B (0.2357%). This indicates that higher configured uplink resources do not necessarily guarantee better transmission quality.

4. Why can A and B use the same SIB1 TDD configuration even though their traffic directions differ?

Because the SIB1 TDD configuration defines the common uplink and downlink resource structure of the cell, not the direction of a particular traffic test. Scenario A uses downlink traffic, while Scenario B uses uplink traffic, even though both use the same DL-heavy TDD configuration.

> More configured UL resources can provide more opportunities for uplink traffic, but they do not guarantee a proportional throughput increase. Scheduling, radio conditions, overhead, offered traffic, and other radio settings also affect the result. This comparison does not establish that the TDD pattern is the only cause of a performance difference. SIB1 does not show actual slot-by-slot data scheduling. Because the configuration captures were recorded separately, use them as references for the supplied settings, not as independent proof of what was broadcast during each original performance run.

### Optional Observation — PRACH Configuration Index

In the same SIB1, find:

```text
servingCellConfigCommon
  → uplinkConfigCommon
    → initialUplinkBWP
      → rach-ConfigCommon
        → setup
          → rach-ConfigGeneric
            → prach-ConfigurationIndex
```

Wireshark may omit or display the `setup` layer differently. You can also use this display filter, then confirm the field is inside SIB1:

```text
nr-rrc.prach_ConfigurationIndex
```

| TDD setting | SIB1 packet number | PRACH configuration index |
| --- | ---: | ---: |
| DL-heavy | 2 | 98 |
| Balanced | 2 | 98 |

PRACH is used to send a random-access preamble. Its configuration index is not the DL/UL slot ratio and does not directly explain iperf3 throughput. Equal indices in the two configurations are a valid observation. Drawing PRACH occasions requires additional parameters and the applicable TS 38.211 table; it is outside the required TDD drawing task.

## 7. Task 5 — Is the TDD Setting Suitable?

Complete the table using the supplied scenario mapping, the SIB1 reference configurations from Task 4, and your throughput results:

| Scenario | TDD setting | Traffic direction | Is the setting suitable for this direction? Why? |
| --- | --- | --- | --- |
| A | DL-heavy | Downlink | Yes. DL-heavy provides more downlink resources, making it suitable for downlink traffic. |
| B | DL-heavy | Uplink | Not ideal. DL-heavy prioritizes downlink resources and may limit uplink transmission opportunities. |
| C | Balanced | Uplink | Yes. Balanced provides more uplink resources than DL-heavy, making it more suitable for uplink traffic. |

Use these ideas:

- More DL resources provide more downlink transmission opportunities.
- More UL resources provide more uplink transmission opportunities.
- A DL-heavy setting is suitable for downlink traffic.
- A DL-heavy setting may limit heavy uplink traffic.
- A balanced setting gives uplink traffic more opportunities than the DL-heavy setting in this lab.

> The configuration captures show the reference broadcast TDD settings. The iperf3 JSON reports performance; it does not contain TDD parameters. The scenario captures are used for traffic analysis and need not include SIB1.

**Question:** Why might Scenario C achieve better uplink throughput than Scenario B? Refer to your SIB1 diagrams and measured results.

Scenario C may achieve better uplink throughput because its Balanced TDD configuration provides more uplink transmission opportunities than the DL-heavy configuration used in Scenario B. According to the SIB1 analysis, Scenario C has a configured uplink symbol share of 45.71%, while Scenario B has only 22.86%. This allows more radio resources to be allocated to uplink transmission. As a result, Scenario C achieves 124.416 Mbps compared to 109.117 Mbps in Scenario B, representing an improvement of approximately 14.02%.

## 8. Task 6 — Find GTP-U Information

Open a **scenario capture** in Wireshark and enter this display filter:

```text
gtp && ip
```

Choose a packet that contains two IPv4 headers. In the packet details, expand:

1. the first `Internet Protocol Version 4` header;
2. `User Datagram Protocol`;
3. `GPRS Tunneling Protocol`; and
4. the second `Internet Protocol Version 4` header.

Record the following information from one packet:

| Item | Value |
| --- | --- |
| Scenario capture filename | scenario-b-dl-heavy-uplink |
| Packet number | 2065 |
| Outer source IP | 192.168.70.129 |
| Outer destination IP | 192.168.70.134 |
| Inner source IP | 10.0.0.6 |
| Inner destination IP | 192.168.70.135 |
| TEID |0x00000012 (18) |

Meaning:

- **Outer IP addresses:** The GTP-U tunnel endpoints.
- **Inner IP addresses:** The original UE/application packet.
- **TEID:** Identifies the GTP-U tunnel at the receiving endpoint.

Take one screenshot showing the selected packet and these fields.
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/357edb91-4939-4c30-89a9-8e791c881b35" />
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/3a3d6e49-2bc9-4384-9a57-486699e27354" />

> Some GTP packets are echo or control-related packets. Select one that contains an inner IPv4 packet.

## 9. Task 7 — TCP Retransmissions

Use the **scenario captures** for this task.

The performance traffic is UDP, but iperf3 also uses a small TCP control connection.

Apply this Wireshark filter:

```text
tcp.analysis.retransmission
```

Record the number of displayed packets:

| Scenario | Displayed TCP retransmissions |
| --- | ---: |
| A | 14 |
| B | 12 |
| C | 12 |

> Report the number displayed by Wireshark using the specified filter. Counts may vary with Wireshark analysis settings. Include screenshots showing the filter and displayed count for each scenario. The filter includes any TCP flows that Wireshark can decode in the capture; a displayed retransmission is not, by itself, proof of loss in the UDP test.
>
a)
<img width="1917" height="428" alt="image" src="https://github.com/user-attachments/assets/2c96b376-5386-445d-a57e-b3cc9554dcf4" />
b)
<img width="1917" height="422" alt="image" src="https://github.com/user-attachments/assets/3ffc6fcb-987b-401f-b375-56713e309425" />
c)
<img width="1917" height="417" alt="image" src="https://github.com/user-attachments/assets/ec74ceac-d25e-43cf-b04c-2598375aad49" />

**Question:** Do these TCP retransmissions directly represent lost UDP performance packets?

No. TCP retransmissions do not directly represent lost UDP performance packets. The performance traffic uses UDP, while TCP retransmissions belong to a separate TCP control connection used by iperf3.
## 10. Task 8 — RTT and Packet Interval

Use a **scenario capture** for both parts of this task.

### TCP RTT

Apply:

```text
tcp.analysis.ack_rtt
```

Select one packet from the iperf3 TCP control connection and record its ACK RTT:

| Scenario | Packet number | Example TCP ACK RTT |
| --- | ---: | --- |
| A| 18.48 | 219 ms|
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/fdabebb0-edb8-4e7f-b7dd-89f28b4279d8" />

This value is the RTT of the TCP control connection. It is not the RTT of the UDP data packets.

### Packet Interval

Apply a display filter to select UDP data packets from the same flow.

Select two consecutive, distinct UDP data packets in the filtered packet list. Avoid duplicated observations caused by merged captures; use the same capture point/interface when possible.

In the packet details of the second packet, find:

```text
Time delta from previous displayed frame
```

Record the scenario, display filter, packet numbers, and time interval.

| Scenario | Display filter | Packet numbers | Time interval |
| --- | --- | --- | --- |
| A | udp && ip.src == 192.168.70.135 && ip.dst == 10.0.0.6 | 1892 → 1896 | 1.045 ms |
<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/8d5de481-e457-49ff-8f6a-b625824088f7" />

**Note:** The time interval depends on the applied display filter. Make sure the selected packets are distinct data packets, not duplicated observations from different capture points. Do not use their interval to infer the TDD slot pattern.

## 11. Final Questions

Answer each question in two or three sentences:

1. Does Scenario A use a TDD setting that matches its traffic direction?

Yes, Scenario A uses a DL-heavy configuration for downlink traffic. This is suitable because most radio resources are assigned to downlink transmission, providing more opportunities to send data.

2. Why is Scenario B expected to have limited uplink resources? Cite a parameter or count from the DL-heavy SIB1 reference configuration.

Scenario B has limited uplink resources because its DL-heavy configuration prioritizes downlink transmission. The SIB1 reference shows only 2 uplink slots out of 10 total slots, resulting in an uplink symbol share of approximately 22.86%.

3. How much does uplink throughput change from B to C?

Uplink throughput increases from 109.117 Mbps in Scenario B to 124.416 Mbps in Scenario C. This represents an increase of approximately 15.30 Mbps, or 14.02%.

4. What negative result, if any, is visible in C? Support your answer with packet loss or jitter values.

Scenario C shows higher packet loss and jitter than Scenario B. Packet loss increases from 0.2357% to 2.1339%, while jitter rises from 0.1162 ms to 0.1620 ms.

5. Why can increasing UL resources improve uplink throughput?

More uplink resources give the UE additional opportunities to transmit data to the network. This allows more data to be sent within the same period, potentially increasing overall throughput.

6. Is adding more UL resources always the best solution? Explain using throughput and packet loss.

No, because increasing uplink resources does not always improve overall network performance. Scenario C achieves 14.02% higher throughput, but packet loss also increases from 0.2357% to 2.1339%, showing a trade-off between speed and reliability.

7. Suggest one simple follow-up test that could improve throughput without producing too much packet loss.

A useful follow-up test would be to keep the Balanced TDD configuration and gradually adjust the iperf3 sending rate using the -b option. This could help find a suitable rate that maintains good throughput while reducing packet loss.

8. What can the configuration captures tell you that the iperf3 JSON cannot? Why are separately recorded configuration captures not direct proof of the configuration during an earlier test?

Configuration captures reveal network parameters such as TDD periodicity, slot allocation, subcarrier spacing, and uplink/downlink symbols, which are not included in iperf3 JSON results. Since these captures were recorded separately, they can serve as reference configurations but cannot confirm exactly which settings were active during the original performance tests.

## 12. Submission Checklist

Submit one Markdown file containing:

- the completed direction table;
- the throughput table and B-to-C calculation;
- the B/C packet-loss and jitter table;
- the SIB1 TDD parameter table, including filenames and packet numbers;
- one SIB1 TDD screenshot per configuration, plus `pattern2` screenshots if needed;
- two TDD diagrams: DL-heavy and balanced, with D/U/F labels and mixed-slot symbol details;
- the per-sequence symbol-count / UL-share table, the common 5 ms comparison table, and the configuration-to-performance table;
- the Task 4.5 answers and the TDD suitability table;
- one GTP-U screenshot and its completed table;
- the TCP retransmission counts and screenshots;
- one TCP ACK RTT example;
- one packet-interval example; and
- answers to the final questions.

The PRACH observation is optional and carries no required points. Include all referenced image files alongside the Markdown file, or submit a ZIP containing the report and images.

## 13. Grading Rubric

| Item | Points |
| --- | ---: |
| Traffic direction | 5 |
| Throughput values and calculation | 20 |
| B/C packet-loss and jitter comparison | 15 |
| SIB1 TDD parameter extraction and screenshot evidence | 10 |
| TDD diagrams, symbol counts, and UL shares | 10 |
| TDD suitability and connection to measured performance | 10 |
| GTP-U fields and screenshot | 15 |
| TCP retransmission, RTT, and packet interval | 10 |
| Clear final answers | 5 |
| **Total** | **100** |

## References

These references support the field names and interpretation. Reading the full specifications is not required.

- [Wireshark NR RRC display filter reference](https://www.wireshark.org/docs/dfref/n/nr-rrc.html): SIB1 and TDD display-filter fields.
- [3GPP TS 38.331, Release 18](https://www.etsi.org/deliver/etsi_ts/138300_138399/138331/18.08.00_60/ts_138331v180800p.pdf): SIB1, TDD-UL-DL-ConfigCommon, and TDD-UL-DL-Pattern definitions.
- [3GPP TS 38.213, Release 17, Section 11.1](https://www.etsi.org/deliver/etsi_ts/138200_138299/138213/17.06.00_60/ts_138213v170600p.pdf): Interpretation of common TDD slot and symbol allocation.
