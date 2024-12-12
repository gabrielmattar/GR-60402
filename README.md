https://github.com/oracle/graal/issues/10275


**Benchmark results on Linux x64:**

```md
| VM                           | Average (ms)      | Min (ms)      | Max (ms)      |
|------------------------------|-------------------|---------------|---------------|
| graalvm-jdk-17.0.13+10.1     | 1.99              | 1.42          | 3.63          |
| jdk-21.0.5                   | 2.05              | 1.63          | 3.08          |
| graalvm-jdk-21.0.5+9.1       | 42.02             | 1.75          | 83.77         |
| graalvm-jdk-23.0.1+11.1      | 28.72             | 1.74          | 87.31         |
``` 

**Describe GraalVM and your environment:**

- **GraalVM version:**
  ```
  java 21.0.5 2024-10-15 LTS
  Java(TM) SE Runtime Environment Oracle GraalVM 21.0.5+9.1 (build 21.0.5+9-LTS-jvmci-23.1-b48)
  Java HotSpot(TM) 64-Bit Server VM Oracle GraalVM 21.0.5+9.1 (build 21.0.5+9-LTS-jvmci-23.1-b48, mixed mode, sharing)
  ```
- **JDK major version:** 21
- **OS:** Ubuntu 24.04.1 LTS
- **Architecture:** x86_64


- **Machine:**
```
Architecture:             x86_64
  CPU op-mode(s):         32-bit, 64-bit
  Address sizes:          46 bits physical, 48 bits virtual
  Byte Order:             Little Endian
CPU(s):                   12
  On-line CPU(s) list:    0-11
Vendor ID:                GenuineIntel
  Model name:             13th Gen Intel(R) Core(TM) i5-1345U
    CPU family:           6
    Model:                186
    Thread(s) per core:   2
    Core(s) per socket:   10
    Socket(s):            1
    Stepping:             3
    CPU(s) scaling MHz:   78%
    CPU max MHz:          4700,0000
    CPU min MHz:          400,0000
    BogoMIPS:             4992,00
    Flags:                fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_
                          tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sd
                          bg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb ssbd ibrs 
                          ibpb stibp ibrs_enhanced tpr_shadow flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb intel_pt
                           sha_ni xsaveopt xsavec xgetbv1 xsaves split_lock_detect user_shstk avx_vnni dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp hwp_pkg_req hfi vnm
                          i umip pku ospke waitpkg gfni vaes vpclmulqdq tme rdpid movdiri movdir64b fsrm md_clear serialize pconfig arch_lbr ibt flush_l1d arch_capabilities
Virtualization features:  
  Virtualization:         VT-x
Caches (sum of all):      
  L1d:                    352 KiB (10 instances)
  L1i:                    576 KiB (10 instances)
  L2:                     6,5 MiB (4 instances)
  L3:                     12 MiB (1 instance)
NUMA:                     
  NUMA node(s):           1
  NUMA node0 CPU(s):      0-11
Vulnerabilities:          
  Gather data sampling:   Not affected
  Itlb multihit:          Not affected
  L1tf:                   Not affected
  Mds:                    Not affected
  Meltdown:               Not affected
  Mmio stale data:        Not affected
  Reg file data sampling: Mitigation; Clear Register File
  Retbleed:               Not affected
  Spec rstack overflow:   Not affected
  Spec store bypass:      Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:             Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:             Mitigation; Enhanced / Automatic IBRS; IBPB conditional; RSB filling; PBRSB-eIBRS SW sequence; BHI BHI_DIS_S
  Srbds:                  Not affected
  Tsx async abort:        Not affected
```



**More details**
It was suggested in the GraalVM Slack to execute the code using the option -Dgraal.CompilationFailureAction=ExitVM, but it did not produce any logs. However, when adding -Dgraal.CompilationBailoutAsFailure=true, we received the following logs (both in GraalVM 17 and GraalVM 21).

```
Thread[JVMCI-native CompilerThread0,5,main]: Compilation of com.sun.crypto.provider.PBKDF2KeyImpl.deriveKey(Mac, byte[], byte[], int, int) @ 198 failed:
jdk.vm.ci.code.BailoutException: Code installation failed: dependencies failed
Failed dependency of type abstract_with_unique_concrete_subtype
  context = *sun.security.provider.DigestBase
  class   = sun.security.provider.SHA2$SHA256
  witness = sun.security.provider.SHA

	at jdk.internal.vm.ci@17.0.13/jdk.vm.ci.hotspot.HotSpotCodeCacheProvider.installCode(HotSpotCodeCacheProvider.java:149)
	at jdk.internal.vm.compiler/org.graalvm.compiler.core.target.Backend.createInstalledCode(Backend.java:205)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.CompilationTask.installMethod(CompilationTask.java:439)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.CompilationTask$HotSpotCompilationWrapper.performCompilation(CompilationTask.java:204)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.CompilationTask$HotSpotCompilationWrapper.performCompilation(CompilationTask.java:109)
	at jdk.internal.vm.compiler/org.graalvm.compiler.core.CompilationWrapper.run(CompilationWrapper.java:224)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.CompilationTask.runCompilation(CompilationTask.java:407)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.CompilationTask.runCompilation(CompilationTask.java:380)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.HotSpotGraalCompiler.compileMethod(HotSpotGraalCompiler.java:159)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.HotSpotGraalCompiler.compileMethod(HotSpotGraalCompiler.java:681)
	at jdk.internal.vm.compiler/org.graalvm.compiler.hotspot.HotSpotGraalCompiler.compileMethod(HotSpotGraalCompiler.java:110)
	at jdk.internal.vm.ci@17.0.13/jdk.vm.ci.hotspot.HotSpotJVMCIRuntime.compileMethod(HotSpotJVMCIRuntime.java:924)
If in an environment where setting system properties is possible, the following
properties are available to change compilation failure reporting:
- To disable compilation failure notifications, set CompilationFailureAction to Silent (e.g., -Dgraal.CompilationFailureAction=Silent).
- To print a message for a compilation failure without retrying the compilation, set CompilationFailureAction to Print (e.g., -Dgraal.CompilationFailureAction=Print).
Retrying compilation of com.sun.crypto.provider.PBKDF2KeyImpl.deriveKey(Mac, byte[], byte[], int, int) @ 198
Dumping IGV graphs in /home/gabriel/playground/benchCrypto/graal_dumps/2024.12.09.13.58.02.738/graal_diagnostics_140234@1/com.sun.crypto.provider.PBKDF2KeyImpl.deriveKey(Mac,_byte[],_byte[],_int,_int)_@_198

```
