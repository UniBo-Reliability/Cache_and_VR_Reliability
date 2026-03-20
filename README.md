General Comments

The folder contains the SPICE netlists with the description of the developed monitoring schemes to detect the occurrence of faults and aging conditions affecting hardware blocks implementing ECCs for caches, and Fully Integrated Voltage Regulators (FIVRs) of high-performance microprocessors using the RISC -V architecture.

More in details, the folder contains the following SPICE netlists:

“Monitor_Hsiao_ECC.cir” includes the electrical level description of our monitoring scheme, that is able to detect the occurrence of faults and aging conditions affecting hardware blocks implementing a Hsiao Single Error Correction – Double Error Detection (SEC-DED) ECC. The netlist includes also the electrical level description of the encoder/decoder blocks of the considered SEC-DED ECC. Both our monitor and the encoder/decoder blocks of the SEC-DED ECC are implemented considering a standard 16nm CMOS technology. 

“Monitor_OLS_ECC.cir” includes the electrical level description of our monitoring scheme, that is able to detect the occurrence of faults and aging conditions affecting hardware blocks implementing an Orthogonal Latin Square (OLS) Double Error Correction (DEC) ECC. The netlist includes also the electrical level description of the encoder/decoder blocks of the considered DEC ECC. Both our monitor and the encoder/decoder blocks of the DEC ECC are implemented considering a standard 16nm CMOS technology.

“Monitor_Voltage_Regulator_FIVR.cir” includes the electrical level description of our monitoring scheme, that is able to detect the occurrence of faults and aging conditions affecting a typical FIVR implementation. The netlist includes also the electrical level description of a typical FIVR. Both out monitor and the FIVR have been implemented considering a standard 22nm CMOS technology.

All netlists in the folder have been written and verified by using a public domain version of SPICE. In particular, we used the LTspice simulation software available at [1]. 

The folder includes also the libraries for the 16nm and 22nm CMOS technologies that have been used to implement the circuits. They come from the Predictive Technology Model (PTM) project, and are freely available at [2]. The files containing these libraries are the “16nm_bulk.lib” for the 16nm CMOS technology (used to implement our monitor for ECCs and the hardware blocks implementing ECCs), and the “22nm.lib” for the 22nm CMOS technology (used to implement our monitor for FIVRs and the case study FIVR).

The folder also includes files “Monitor_Hsiao_ECC.plt”, “Monitor_OLS_ECC.plt” and “Monitor_Voltage_Regulator_FIVR.plt”, which specify which waveforms to plot once SPICE simulations are concluded. 

It should be noted that, due to the complexity of the circuits, the initial Newton-Raphson iterations and the preliminary Pseudo-Transient analyses may require a non-negligible simulation time. These initial steps can be skipped without losing accuracy by pressing the ESC key, before the beginning of Transient analysis. This procedure is also suggested in the LTspice bar appearing at the bottom of the window during simulations. 

Finally, to emulate aging conditions, we considered the effects of Bias Temperature Instability (BTI), which is recognized as the main aging mechanism for modern high performance microprocessors. In order to evaluate the effects of BTI, we properly modified the value of the nMOS and pMOS threshold voltage parameter VTHON and VTHOP, respectively. We derived the values for such parameters due to BTI degradation, for some specific circuit operating times (i.e., for 1, 2, 3, 4, 5, 6, 7, 8, 9 and 10 years), by means of the HSPICE MOSRA tool [3]. The obtained values of VTHON and VTHOP for degraded transistors are reported in the libraries “16nm_bulk.lib” and “22nm.lib”, as a list of commented values. In order to emulate BTI degradation at a given operating time, it is necessary to select the corresponding values of VTHON and VTHOP from such a list for the desired operating time. 



Brief Description of Our Monitor for Faults and Aging Conditions Affecting ECC Hardware Blocks 

The netlist “Monitor_Hsiao_ECC.cir” describes the electrical level implementation of our monitor to detect the occurrence of faults and aging conditions affecting hardware blocks implementing the Single Error Correcting – Double Error Detecting (SEC-DED) Hsiao ECC [4]. Similarly, the netlist “Monitor_OLS_ECC.cir” describes the electrical level implementation of our monitor to detect the occurrence of faults and aging conditions affecting hardware blocks implementing the Double Error Correcting (DEC) Orthogonal Latin Square (OLS) ECC [5, 6]. It should be noted that our monitor can be applied also to other different ECCs by straightforward modifications.

In order to verify the operation of the proposed monitors, the netlists also include the description of the encoding/decoding blocks of the considered ECCs. For the two ECCs, we considered a realistic case of 16 information bits (d0, …, d15) that, for the case of the SEC-DED Hsiao ECC, require the generation of 6 check bits (c0, …, c5), while for the case of the DEC OLS ECC require the generation of 16 check bits (c0, …, c15). 

As for faults affecting the encoding/decoding blocks of the ECCs, we considered all possible resistive bridging (BFs), with values of connecting resistance (RB) in the interval ]0Ω,100kΩ], and stuck-at faults (SAs). As shown in [7], we observed that the 68% (61%) of the considered faults affecting the SEC-DED Hsiao (OLS) ECC encoding/decoding blocks are critical, since they may either inhibit the ECC correction of incorrect words read from the cache, or introduce errors in otherwise correct words read from the cache, with consequent risks for microprocessor reliability. 

As for aging conditions, we analyzed the impact of BTI on the propagation delay of the encoding/decoding blocks of the two ECC case studies, considering a cache operating time between 0 and 7 years. We found out that BTI may significantly degrade the propagation delay of the considered ECCs’ encoding/decoding blocks (by more than 20% over 10 years of circuit lifetime), with possible risks for microprocessor reliability.

In the following paragraphs we briefly describe our monitor for the Hsiao SEC-DED ECC only, since its adoption for the OLS DEC ECC is identical.  

Our monitoring scheme consists of two main blocks: 1) a Fault Detector block (FDetector) to detect critical faults affecting the hardware blocks implementing the ECCs; 2) an Aging Detector block (ADetector) to detect the occurrence of aging conditions affecting hardware blocks implementing the ECCs.

As for FDetector, it receives the words read from the cache (d0, ..., d15, c0, ..., c5) and the outputs of the ECC Decoder (dcorr0, ..., dcorr15), and it generates two output signals (EC1, EC2).

More in details, FDetector compares: 1) the information bits in the word read from the cache (d0, …, d15) with the Decoder output (dcorr0, …, dcorr15); 2) the check bits read from the cache (c0, …, c5) with check bits regenerated by FDetector (c0R, …, c5R) starting from the information bits in the word read from the cache. Then, FDetector makes (EC1, EC2) = (0, 1) or (1, 0) if the words compared in 1) and 2) are the same, or it makes (EC1, EC2) = (0, 0) or (1, 1) otherwise.

In order to detect the presence of faults affecting the ECC blocks, signals (EC1, EC2) should be analyzed together with the error signal ErrorDet generated by the Decoder.

In particular, the condition of fault-free ECC hardware blocks will be identified by one of the following configurations:

(EC1, EC2, ErrorDet) = (0, 0, 1) or (1, 1, 1), or (EC1, EC2, ErrorDet) = (0, 1, 0) or (1, 0, 0).

In fact, in the absence of faults and in the presence of no error in the word read from the cache, it is ErrorDet = 0, and FDetector makes (EC1, EC2) = (0, 1) or (1, 0), depending on the word read from the cache. The condition (EC1, EC2) = (0, 1) or (1, 0) is consistent with the indication ErrorDet=0, so this combination is recognized as an indication of absence of faults. Instead, in case of an incorrect word read from the cache, it is ErrorDet = 1, and FDetector makes (EC1, EC2) = (0, 0) or (1, 1), since (d0, …, d15) ≠ (dcorr0, …, dcorr15) or (c0, …, c5) ≠ (c0R, …, c5R).

Instead, the condition of faults affecting the ECC hardware blocks will be identified by one of the following configurations:

(EC1, EC2, ErrorDet) = (0, 1, 1) or (1, 0, 1), or (EC1, EC2, ErrorDet) = (0, 0, 0) or (1, 1, 0).

In fact, in case of faults that inhibit the ECC’s correction ability it is ErrorDet = 1 (indicating that the word read from the cache is erroneous), but the word at the Decoder output is the same erroneous word read from the cache, so that FDetector makes (EC1, EC2) = (0, 1) or (1, 0). Instead, in case of faults introducing errors in correct words read from the cache, it is ErrorDet = 0 (indicating a correct word read from the cache), but the word at the Decoder output is different from the correct word read from the cache, so that FDetector makes (EC1, EC2) = (0, 0) or (1, 1).

A more detailed analysis of the effects of faults affecting the ECC hardware blocks, and the description of the FDetector have been presented in [7].

Instead, as for  ADetector, it is able to detect the occurrence late transitions occurring at the outputs of the Decoder (dcorr0, ..., dcorr15) due to BTI degradation affecting the hardware blocks of the ECCs.

In particular, ADetector monitors the occurrence of late transitions on such Decoder outputs during a proper time guardband, before the setup time of the flip-flops sampling the Decoder outputs. This way, ADetector enables to generate an alarm signal before that late transitions on the Decoder outputs can reach the flip-flops’ setup time, thus resulting in an incorrect sampling, with consequent risks for the microprocessor reliability. The goal to generate an alarm signal before that BTI degradation results in a wrong sampling by the flip-flops at the Decoder outputs, is to use such an alarm signal as a trigger for strategies that enable to mitigate the effects of BTI degradation on ECCs hardware blocks, thus avoiding future risks for the microprocessor reliability.

More in details, ADetector receives the Decoder outputs (dcorr0, ..., dcorr15), and an external signal Time Window Control (TWC), which is equal to 1 only during the predefined time guardband (before the setup time of the flip-flops sampling the Decoder outputs). The ADetector generates two signals (AC1, AC2) encoded by the two-rail code.

In particular, outside the time guardband (when TWC=0), ADetector is disabled and (AC1, AC2) = (0,1) or (1,0).

Instead, during the time guardband (when TWC=1), ADetector is enabled to detect late transitions at the Decoder outputs. In this case, if the Decoder outputs are stable while TWC=1, our ADetector makes (AC1, AC2) = (0,1) or (1,0), thus indicating that the ECC hardware blocks are not affected by aging conditions. Instead, if at least one of the Decoder outputs presents a transition while TWC=1, our ADetector generates an alarm message at its outputs, that is (AC1, AC2) = (0,0) or (1,1), thus indicating that the ECC hardware blocks are affected by aging conditions. 

A detailed description of the effects of aging affecting the ECC blocks and the description of our ADetector are described in a paper that will soon be submitted to an International Journal.

It should be noted that, for convenience, all circuits described in the netlists “Monitor_Hsiao_ECC.cir” and “Monitor_OLS_ECC.cir” have been considered non-aged and fault-free.

In order to verify the ability of our FDetector in detecting faults affecting the ECC hardware blocks it is necessary to uncomment one of the resistive bridgings (Rbf0 …. Rbf15) affecting the Decoder outputs. 

Similarly, in order to verify the ability of ADetector to detected the presence of aged ECC blocks it is necessary to change the values of transistors parameters VTHON and VTHOP in the library “16nm_bulk.lib” (as described in the general comments above), in order to emulate the presence of BTI. 

For simplicity, in the netlists we emulated the presence of a single word read from the cache. For the Hiao SEC-DED ECC it is (d0, ..., d15, c0, ..., c5) = (0,1,1,0,1,0,1,1,0,1,0,0,0,1,0,1,0,1,0,1,1,1), while for the OLS DEC ECC it is (d0, ..., d15, c0, ..., c5) = (0,1,1,0,1,0,1,1,0,1,0,0,0,1,0,1,0,1,1,0,1,1,0,0,1,0,1,0,0,0,0,0). Moreover, for both ECCs, in the netlists we emulated the occurrence of an error on the bit d4 at the second read operation (i.e., at the second CK cycle), in order to verify the operation of FDetector in the fault-free case. 




Brief Description of Our Monitor for Faults and Aging Conditions Affecting Fully Integrated Voltage Regulators (FIVRs) 

The netlist “Monitor_Voltage_Regulator_FIVR.cir” describes the electrical level implementation of our monitoring scheme to detect the occurrence of faults and aging conditions affecting Fully Integrated Voltge Regulators (FIVRs). The monitor implemented in the netlist has been described in [8].

In order to verify the operation of the proposed monitor, the netlist also includes the implementation of a case study FIVR. It has been implemented as the FIVR described in [9], that is representative of FIVRs currently adopted in high performance microprocessors/SoCs. The FIVR is implemented in the sub-circuit FIVR; it receives a reference voltage Vref and produces an output voltage Vout, which changes dynamically based on the Vref value. 

Both our monitor and the FIVR have been implemented using the same 22nm CMOS technology, which is the same technology considered in [9].

As for faults affecting the FIVR, we considered all possible: transistor Stuck-Opens (SOPs), transistor Stuck-ONs (SONs), and resistive Bridging Faults (BFs) with values of connecting resistance (RB) in the interval ]0Ω,100kΩ]. Moreover, we realistically assumed a Vout tolerance margin (i.e., a maximum tolerable percentage variation of Vout with respect to the desired Vref) of ±5%, in order to guarantee the microprocessor correct operation. As we shown in [8], we observed that the 40.9% of the considered FIVR faults do not produce any significant effect on the produced Vout, while the 59.1% result in a Vout that is outside the considered tolerance margin. These latter 59.1% of FIVR faults are therefore critical for the microprocessor/SoC operation.

As for aging conditions, we analyzed the impact of BTI on the produced Vout, considering a FIVR operating time between 0 and 10 years. In particular, we analyzed the impact of BTI on the increase of the FIVR response time to Vref changes. In this regard, we measured the FIVR response time as the time required by Vout to reach the 95% of the new Vref value (accounting also for the previously defined 5% tolerance margin). In addition, we assumed a maximum tolerable FIVR response time of 10% higher than the FIVR response time at time 0 (i.e., no aging). 
As shown in [8], BTI does not affect the ability of the FIVR to produce a correct Vout, but it induces an increase in the FIVR response time of approx. 18% over the considered 10 years of operation, with possible risks for the microprocessor/SoC correct operation in the field. 

In order to detect the identified critical faults and aging conditions affecting the FIVR, we proposed the monitor scheme implemented in the netlist “Monitor_Voltage_Regulator_FIVR.cir” and presented in [8]. More in details, our monitor enables to detect: 1) the generation of a FIVR Vout falling outside the considered tolerance margin of 5% with respect to Vref; 2) a FIVR response time in changing Vout after a change in Vref (degraded due to BTI) that is 10% higher than the FIVR response time at the beginning of circuit operation (i.e., FIVR with no aging).

The monitor receives as inputs: i) the reference voltage Vref; ii) the FIVR output voltage Vout; iii) a synchronization signal RefChg, that presents a pulse each time the value of Vref is changed; iv) a reset signal (Rs) to reset error indications produced by our monitor.

The monitor produces two outputs Err1 and Err2 encoded by the Two-Rail code. In particular, if the FIVR Vout is inside the tolerance margin and if the FIVR response time is lower than the maximum tolerable response time, the outputs Err1 and Err2 assume alternating logic values, that are to each other complemented (i.e., (Err1, Err2) = (1, 0) or (0, 1)). Instead, if Vout is outside the tolerance margin, or if the response time becomes higher than the 10% of the FIVR response time at the beginning of circuit operation, our monitor makes (Err1, Err2) = (0, 0) or (1, 1) until the reset signal (Rs) is asserted.

It should be noted that, for convenience, all circuits described in the netlist “Monitor_Voltage_Regulator_FIVR.cir” have been considered non-aged and fault-free. 

In order to verify the ability of our monitor to detect faults affecting the FIVR, it is necessary to emulate one of the considered faults affecting the FIVR. As a straightforward example, one of the transistors composing the FIVR (in the sub-circuit FIVR) could be commented, in order to emulate the occurrence of a Stuck-Open on such a transistor. Otherwise, one of the transistors composing the FIVR could be replaced by a resistor with a low resistance value, in order to emulate the occurrence of a Stuck-On on such a transistor. Finally, resistors connecting two internal nodes of the FIVR could be added to emulate the occurrence of bridging faults. 

Similarly, in order to verify the ability of our monitor to detected a degraded FIVR response time, it is necessary to change the values of transistors parameters VTHON and VTHOP in the library “22nm.lib” (as described in the general comments above), in order to emulate the presence of BTI. 

In this regard, as an example, the value of Vref is changed three times during simulation time, in order to be able to evaluate the ability of the monitor to detect a FIVR degraded response time (due to BTI) exceeding the maximum tolerable limit. 




References

[1] 	https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html

[2] 	https://mec.umn.edu/ptm.

[3] 	B. Tudor, J. Wang, W. Liu, H. Elhak, “MOS Device Aging Analysis with HSPICE and CustomSim”, Synopsys, August 2011.

[4] 	M.H. Hsiao, “A Class of Optimal Minimum odd-weight-column SEC-DED Codes”, IBM Journal of Research and Development, Vol. 14, No. 4, 1970, pp. 395,401.

[5] 	D.C. Bossen, “b-Adjacent Error Correction”, IBM Journal of Research and Development, Vol. 14, No. 4, 1970, pp. 402-408.

[6] M. Hsiao, D.C. Bossen, R.T.  Chien, “Orthogonal Latin Square Codes”, IBM Journal of Research and Development, Vol. 14, 1970. 

[7] 	M. Omaña, A. Manfredi, C. Metra, R. Locatelli, M. Chiavacci, S. Petrucci, “Silent Data Corruption and Reliability Risks due to Faults Affecting High Performance Microprocessors’ Caches”, in Proc. of IEEE Int. Symp. on On-Line Testing and Robust System Design, 2024.

[8] 	M. Omaña, A. Menghi, A. Stefani, E. Vicini, C. Metra, G. Froio, S. Petrucci “On-Line Test of Fully Integrated Voltage Regulators for High Performance Microprocessors of Autonomous Systems”, in Proc. of IEEE Int. Symp. on On-Line Testing and Robust System Design, 2025.

[9] 	E. Burton, G. Schrom, F. Paillet, J. Douglas, W. Lambert, K. Radhakrishnan M. Hill, “FIVR — Fully Integrated Voltage Regulators on 4th Generation Intel® Core™ SoCs”, in Proc. of IEEE Applied Power Electronics Conference and Exposition – APEC, 2014, pp. 432 – 439.
