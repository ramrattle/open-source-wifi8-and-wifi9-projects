# Open-source Wi-Fi 7 and Wi-Fi 8 projects

An evidence-oriented map of software, simulators, research code, and technical
communities working on **Wi-Fi 7 (IEEE 802.11be, Extremely High Throughput)**
and **Wi-Fi 8 (IEEE 802.11bn, Ultra High Reliability)**.

> **Research snapshot:** 6 November 2026. “Open source” here means that source
> code is publicly available under an identifiable open-source licence. A
> public paper, standards page, firmware image, product SDK, or issue tracker
> alone is not counted as an open-source implementation.

## What's new

### November 2026

- **Linux:** initial IEEE 802.11bn (UHR) support has reached mainline
  `mac80211`/`cfg80211`. [`net/mac80211/uhr.c`](https://github.com/torvalds/linux/blob/master/net/mac80211/uhr.c)
  parses peer UHR capabilities, and follow-up series on linux-wireless add
  `mac80211_hwsim` UHR test support and draft elements such as DPS, DBE, and
  P-EDCA. All of it follows draft amendment text and will change with it.
- **hostapd:** most of a UHR patch series was applied in May 2026 behind the
  `CONFIG_IEEE80211BN` build option, using IEEE P802.11bn/D1.4 element
  definitions. hwsim test cases are held back until the kernel side is upstream.
- **OpenWrt 25.12:** the stable series (first released 5 March 2026; 25.12.6 on
  18 October 2026) brings MediaTek MT7996/MT7992 MLO link fixes and per-link
  beacon monitoring. 25.12.6 also fixes a hostapd/wpa_supplicant MLO parsing
  flaw that unauthenticated frames could trigger.
- Added the Kom8ndor paper (arXiv 2606.25435) to the Komondor entry.

### August 2026

- Added Komondor v4/Kom8ndor, which implements 802.11bn-oriented multi-AP
  coordination, dynamic subband operation, and non-primary channel access.
- Added the JAXWiFi ecosystem for reproducible, learning-based coordinated
  spatial-reuse experiments and its commodity-hardware Co-SR testbed.

## How to read this list

The maturity labels prevent a research model, a driver, and a complete network
stack from being presented as equivalent:

- **Production** — maintained code used on real hardware.
- **Simulator** — executable protocol/model code, not hardware firmware.
- **Research prototype** — useful experimental code without a production claim.
- **Enabling infrastructure** — necessary for deployment, but not by itself an
  802.11be/802.11bn implementation.
- **Community/specification** — a place to follow or contribute; not a codebase.

Feature support is branch, kernel, driver, chipset, and firmware dependent.
In particular, a repository supporting one EHT or MLO feature does **not** imply
complete Wi-Fi Alliance certification, and an open driver does not make the
device firmware open.

---

## Wi-Fi 7 / IEEE 802.11be (EHT)

Wi-Fi 7's defining implementation topics include 320 MHz channels, 4096-QAM,
multi-link operation (MLO), multiple resource units, puncturing, and EHT PHY/MAC
operation. The strongest open-source coverage today is in Linux, hostapd, and
ns-3.

### Hardware-facing implementations

| Project | Type | What is relevant to Wi-Fi 7 | Practical caveats |
|---|---|---|---|
| [Linux wireless (`mac80211`, `cfg80211`, `nl80211`)](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/net/wireless) | Production | Shared upstream EHT/MLO data structures, channel definitions, station/link handling, and userspace API used by Wi-Fi 7 drivers. [Source tree](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/include/uapi/linux/nl80211.h) and [wireless-next development tree](https://git.kernel.org/pub/scm/linux/kernel/git/wireless/wireless-next.git/). | Capability depends on the driver and usually closed device firmware. Use wireless-next to study development and a released kernel for deployment. Initial 802.11bn (UHR) code is now in mainline; see the Wi-Fi 8 section. |
| [ath12k](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/drivers/net/wireless/ath/ath12k) | Production | Upstream Qualcomm Wi-Fi 7-class PCI/AHB driver; EHT, 6 GHz, and ongoing MLO work use the common Linux wireless stack. Development is reviewed on [linux-wireless](https://lore.kernel.org/linux-wireless/). | Host driver is GPL, but required board data and device firmware are separate; chipset and kernel support varies. |
| [MediaTek `mt76`](https://github.com/openwrt/mt76) | Production | GPL driver family used by OpenWrt and Linux; the `mt7996` code supports EHT-capable MediaTek hardware and is a particularly accessible route for router experiments. [Upstream Linux copy](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/drivers/net/wireless/mediatek/mt76/mt7996). | Firmware remains a separate binary and the OpenWrt mirror may move ahead of or behind a kernel release. |
| [Intel `iwlwifi`](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/drivers/net/wireless/intel/iwlwifi) | Production | Open host driver for Intel adapters, with EHT definitions and support for applicable Wi-Fi 7 generations. Intel also publishes the [backport-iwlwifi](https://github.com/intel/backport-iwlwifi) development/backport tree. | Firmware is redistributable binary, not open source; exact EHT/MLO capability is adapter and firmware dependent. |
| [hostapd / wpa_supplicant](https://w1.fi/hostapd/) ([official git](https://w1.fi/cgit/hostap/)) | Production | Reference open userspace AP/authenticator and station/supplicant. EHT capability/configuration and MLO control are developed here and exposed through drivers such as `nl80211`. | Supporting configuration syntax is not proof that a driver/firmware combination implements it. Check the git version rather than an old distribution package. |
| [OpenWrt](https://github.com/openwrt/openwrt) | Production distribution | Integrates Linux, `mac80211`, hostapd, drivers, regulatory data, and router configuration. Its [packages](https://github.com/openwrt/openwrt/tree/main/package/network) and build system make reproducible Wi-Fi 7 AP testbeds possible on supported targets. | OpenWrt is integration rather than a new 802.11be MAC/PHY. Hardware support and acceleration can require vendor components. The 25.12 stable series is the current baseline for MediaTek Wi-Fi 7/MLO testbeds, and 25.12.6 (October 2026) carries a security fix for MLO element parsing in hostapd/wpa_supplicant. |
| [Linux wireless regulatory database](https://git.kernel.org/pub/scm/linux/kernel/git/wens/wireless-regdb.git/) | Enabling infrastructure | Open regulatory rules consumed by Linux/CRDA tooling, essential when testing 6 GHz and 320 MHz operation legally. | It does not implement Wi-Fi 7 and cannot override local law, AFC requirements, or hardware restrictions. |
| [Open AFC](https://github.com/open-afc-project/openafc) | Enabling infrastructure | Open automated-frequency-coordination system for standard-power 6 GHz devices; relevant to outdoor/high-power Wi-Fi 7 deployments in AFC regimes. | AFC is a spectrum-access service, not an 802.11be implementation, and deployment rules are jurisdiction-specific. |

### Broadband gateways, RDK-B, prpl, and managed Wi-Fi

Wi-Fi 7 gateways normally contain another layer above Linux and hostapd: a
broadband data model, hardware-abstraction API, mesh controller/agent, telemetry,
and remote-management service. The projects below are included only where they
provide that Wi-Fi-facing layer. Generic WAN, DOCSIS, container, voice, and cloud
projects from the same ecosystems are deliberately excluded.

| Project | Broadband role | Wi-Fi 7 relevance | Scope and caveats |
|---|---|---|---|
| [RDK-B OneWifi](https://github.com/rdkcentral/OneWifi) | RDK-B Wi-Fi management component | RDK's consolidated Wi-Fi software layer for residential gateways. It connects RDK management/data-model behavior to the platform Wi-Fi HAL and is the RDK-B component to inspect for EHT-, MLO-, radio-, VAP-, and station-facing work. | OneWifi is not a PHY, driver, or firmware. Actual Wi-Fi 7 features require a matching RDK platform port, Wi-Fi HAL, vendor driver/firmware, hostap integration, and hardware. Verify the target branch and HAL API rather than assuming every RDK image supports 802.11be. |
| [RDK-B Wi-Fi HAL interface](https://github.com/rdkcentral/rdkb-halif-wifi) | Vendor-neutral hardware-abstraction contract | Defines the interface between RDK Wi-Fi management and chipset/platform implementations. This is where representations needed by newer radios and multi-link capabilities must be exposed consistently. | An interface repository is not a working radio implementation. Vendor HAL implementations and firmware may be proprietary, target-specific, or maintained outside this repository. |
| [RDK-B `CcspWifiAgent`](https://github.com/rdkcentral/rdkb-CcspWifiAgent) | Legacy/companion TR-181 Wi-Fi management agent | Useful when tracing deployed RDK-B data-model behavior and the migration from older Wi-Fi-agent architecture to OneWifi on broadband gateways. | Do not count it as native Wi-Fi 7 solely because it manages a Wi-Fi device; architecture and supported objects vary by RDK release and product. Prefer OneWifi for new platform investigation. |
| [prplOS](https://github.com/prplfoundation/prplOS) | Open gateway distribution and build system | Operator-oriented Linux/OpenWrt-derived platform that integrates hostapd, kernel drivers, data models, and vendor targets for modern broadband gateways, including capable Wi-Fi 7 hardware when the target supplies it. | Integration platform, not an independent 802.11be MAC/PHY. A prplOS build is Wi-Fi 7 only when its target, driver, firmware, hostap, and management stack expose the required EHT/MLO functions. |
| [prplMesh](https://github.com/prplfoundation/prplMesh) | Open Wi-Fi EasyMesh controller and agent | Multi-AP topology, onboarding, steering, metrics, and policy/control functions relevant to operator-managed Wi-Fi 7 mesh networks. Later EasyMesh profiles are the place to track multi-link and Wi-Fi 7 management work. | EasyMesh and 802.11be are different specifications. prplMesh can manage Wi-Fi 7 access points without implementing their PHY/MAC, and support must be checked feature-by-feature against the implemented EasyMesh profile. |
| [prpl Foundation hostap fork](https://github.com/prplfoundation/hostap) | Gateway-oriented hostap integration | Carries hostapd/wpa_supplicant integration used in prpl gateway work and can expose EHT/MLO controls needed by the mesh and management layers. | Prefer changes accepted by upstream hostap when possible. Fork-only patches, platform patches, and vendor driver behavior can differ from upstream. |
| [OpenSync](https://github.com/plume-design/opensync) | Cloud-managed residential Wi-Fi agent | Open device-side management framework used on broadband gateways and mesh nodes; its OVSDB schemas, target layer, steering, statistics, and topology services can manage Wi-Fi 7-capable platforms. | OpenSync is not itself an EHT implementation. Wi-Fi 7 depends on the target adaptation and underlying SDK/driver, and some production cloud or target components may not be open. |
| [Broadband Forum OB-USP-Agent](https://github.com/BroadbandForum/obuspa) | Open TR-369 USP agent | Provides standards-based remote lifecycle/configuration plumbing through which a gateway can expose current TR-181 Wi-Fi objects and vendor extensions for Wi-Fi 7 devices. | It is a generic USP agent, not a Wi-Fi stack. Whether EHT/MLO is manageable depends on the installed TR-181 data-model implementation and component bindings. |

For RDK-B specifically, follow the call path **TR-181/USP or WebPA → RBUS/CCSP
component → OneWifi → Wi-Fi HAL → driver/firmware**. A Wi-Fi 7 field at the
management layer is useful only if every lower layer implements it. For prpl,
the comparable path is **USP/data model or prplMesh → hostap/platform services →
nl80211/driver → firmware**. These paths also explain why neither framework
should be described as “an open-source Wi-Fi 7 driver.”

### Simulators and research code

| Project | Type | Wi-Fi 7 coverage and useful entry points | Limits |
|---|---|---|---|
| [ns-3](https://gitlab.com/nsnam/ns-3-dev) | Simulator | The mainline Wi-Fi model includes EHT PHY/MAC, 320 MHz operation, EHT MCSs, puncturing, multi-link devices, link selection, and multi-link traffic. Start with the [Wi-Fi model manual](https://www.nsnam.org/docs/models/html/wifi.html), [`wifi-eht-network`](https://gitlab.com/nsnam/ns-3-dev/-/blob/master/examples/wireless/wifi-eht-network.cc), and the [Wi-Fi examples directory](https://gitlab.com/nsnam/ns-3-dev/-/tree/master/examples/wireless). | Packet-level abstraction, not bit-accurate RF or a certification oracle. Check the manual for each model assumption and the release notes for the version in use. |
| [ns3-ai](https://github.com/hust-diangroup/ns3-ai) | Research infrastructure | Connects ns-3 to Python/ML frameworks. It can wrap EHT/MLO ns-3 scenarios for learned scheduling, link selection, or rate-control experiments. | It contributes the ML bridge, not an independent 802.11be model; reproducibility depends on the scenario and ns-3 revision. |
| [5G-EmPOWER](https://github.com/5g-empower/5g-empower) | Research prototype | Open programmable WLAN control platform useful for experiments on radio/network control concepts that extend into coordinated Wi-Fi 7 deployments. | Its original WLAN agents predate 802.11be. Count it as control-plane research infrastructure, not native EHT/MLO support. |

Repositories sometimes advertise “Wi-Fi 7 simulation” while only changing a
PHY rate constant in an 802.11ax model. For defensible results, prefer ns-3
mainline, record the exact commit, publish the configuration and random seeds,
and state which EHT features are actually modeled.

### Research and contributor communities

- **[IEEE 802.11 Task Group be](https://www.ieee802.org/11/Reports/tgbe_update.htm)** — official public status, meeting reports, timelines, and links for the EHT amendment. IEEE standards drafts/final text may require access and are not open-source software.
- **[ns-3 Wi-Fi module](https://www.nsnam.org/wiki/Wi-Fi)** and **[ns-3 users list](https://groups.google.com/g/ns-3-users)** — model design, validation, examples, and review. Development occurs through the [ns-3 GitLab](https://gitlab.com/nsnam/ns-3-dev/-/issues) merge-request/issue workflow.
- **[Linux wireless](https://wireless.wiki.kernel.org/)** and **[linux-wireless archive](https://lore.kernel.org/linux-wireless/)** — upstream discussion for EHT/MLO APIs, `mac80211`, and drivers. This is the best primary trail for determining whether a claimed feature actually reached upstream.
- **[OpenWrt developer community](https://openwrt.org/contact)** — device integration, hostapd packaging, driver testing, and reproducible router builds.
- **[Wi-Fi Alliance Wi-Fi CERTIFIED 7](https://www.wi-fi.org/discover-wi-fi/wi-fi-certified-7)** — interoperability/certification context. The Alliance is not an open-source development project.

### Open research background

- López-Pérez et al., [“IEEE 802.11be Extremely High Throughput: The Next Generation of Wi-Fi Technology Beyond 802.11ax”](https://arxiv.org/abs/1902.04320) — early architectural survey and requirements.
- Adame et al., [“Time-Sensitive Networking in IEEE 802.11be: On the Way to Low-Latency WiFi 7”](https://doi.org/10.3390/s21154954) — open-access latency and TSN analysis.
- ns-3’s [Wi-Fi design documentation](https://www.nsnam.org/docs/models/html/wifi-design.html) — the most useful companion when auditing what the executable EHT model abstracts.

---

## Wi-Fi 8 / IEEE 802.11bn (UHR)

Wi-Fi 8 is aimed primarily at **ultra-high reliability**, including more
predictable latency and better operation at range and under interference,
rather than simply a higher headline peak rate. Candidate work areas include
multi-AP coordination, improved multi-link operation, seamless mobility,
reliability enhancements, and more efficient spectrum use. Because 802.11bn is
still under development, the open-source landscape is much earlier and claims
need especially careful qualification.

### Native Wi-Fi 8 projects

| Project | Type | What exists now | Maturity warning |
|---|---|---|---|
| [Komondor / Kom8ndor](https://github.com/wn-upf/Komondor) ([paper](https://arxiv.org/abs/2606.25435)) | Simulator | Event-driven 802.11bn-oriented simulator with Co-TDMA, coordinated spatial reuse, coordinated beamforming, dynamic subband operation, and non-primary channel access. Version 4 is the first Kom8ndor release; the code includes build instructions, examples, regression tests, and a GPL-3.0 licence. | The older core was validated against ns-3 and analytical models, but each newly added 11bn mechanism still requires feature-specific validation. It is a research simulator, not draft-conformance or interoperability evidence. |
| [JAXWiFi / `ml4wifi-devs`](https://github.com/ml4wifi-devs) ([interactive demo](https://github.com/ml4wifi-devs/mapc-demo)) | Research ecosystem | Reproducible JAX-based coordinated-spatial-reuse simulation plus hierarchical bandits, optimization bounds, metaheuristics, GNN surrogates, generative scheduling, DCF/SR baselines, and a browser comparison tool. The individual repositories expose code, tests or examples under CC0-1.0. | Models the 802.11bn multi-AP coordination problem rather than the complete amendment. Results depend on the TGax-derived channel abstraction, selected scheduler, and exact revisions of the component repositories. |
| [`mapc-testbed`](https://github.com/ml4wifi-devs/mapc-testbed) | Research testbed | Commodity AR9271-based coordinated-spatial-reuse platform that moves timed frame release into open ath9k_htc firmware, aligns distributed AP transmissions using TSF, and measures delivery, timing error, and signal strength. | Uses older 802.11n-class 2.4 GHz radios to study a future MAPC mechanism. It demonstrates real over-the-air coordination behavior, not a Wi-Fi 8 PHY, complete 802.11bn MAC, or certified device. |
| [ns-3 mainline Wi-Fi](https://gitlab.com/nsnam/ns-3-dev/-/tree/master/src/wifi) | Simulator foundation | The open place most likely to receive reviewed 802.11bn models. Existing EHT/MLO, channel, interference, queueing, roaming, and multi-AP scenario machinery provides a baseline for UHR research. Follow [Wi-Fi issues](https://gitlab.com/nsnam/ns-3-dev/-/issues/?label_name%5B%5D=wifi) and release notes for features that actually land. | **Not a complete 802.11bn model.** Do not relabel an EHT scenario as Wi-Fi 8. A result is 11bn-specific only when it implements a defined UHR mechanism and documents assumptions. |
| [Linux wireless-next](https://git.kernel.org/pub/scm/linux/kernel/git/wireless/wireless-next.git/) | Production-development foundation | Upstream staging point for future `cfg80211`/`mac80211` and driver changes. Mainline now carries initial UHR capability handling (`net/mac80211/uhr.c`), and wireless-next series add `mac80211_hwsim` UHR support, DPS/DBE/P-EDCA element definitions, and Seamless Mobility Domain work based on P802.11bn drafts. | **No claim of complete Wi-Fi 8 support.** Early patches, vendor features, or an “11bn” constant do not constitute interoperability. |
| [hostap development tree](https://w1.fi/cgit/hostap/) | Production-development foundation | Userspace home for UHR negotiation, AP coordination/control, and enhanced MLO behavior. A first UHR patch series was largely applied in May 2026 behind `CONFIG_IEEE80211BN`, using P802.11bn/D1.4 definitions; see the [hostap list thread](http://lists.infradead.org/pipermail/hostap/2026-May/045188.html). | **No claim of complete Wi-Fi 8 support.** Track commits and mailing-list review rather than product announcements. |

**Bottom line:** there is not yet a broadly accepted, production-ready,
end-to-end open-source Wi-Fi 8 stack comparable to the Linux + hostapd Wi-Fi 7
path. Public GitHub repositories that contain slides, paper PDFs, datasets,
rate calculators, or renamed 802.11ax/11be simulations should not be listed as
implementations. This short native-project list is intentional.

### Reusable open-source foundations for Wi-Fi 8 research

These projects can support an 802.11bn experiment, but none should be cited as
implementing the standard without additional code and validation.

| Project | Research use | What must be added or demonstrated |
|---|---|---|
| [ns-3](https://gitlab.com/nsnam/ns-3-dev) | Packet-level evaluation of coordinated APs, MLO reliability, latency tails, roaming, scheduling, and interference. | The proposed UHR algorithm/protocol, draft-aligned signaling, validation, and explicit versioned assumptions. |
| [Mininet-WiFi](https://github.com/intrig-unicamp/mininet-wifi) | Real Linux namespaces, virtual stations/APs, mobility, SDN control, and repeatable topology experiments. | It emulates network behavior; it needs a suitable radio/channel abstraction or actual compatible radios for 802.11bn claims. |
| [Sionna](https://github.com/NVlabs/sionna) | Open-source, GPU-accelerated link-level communications research, ray tracing, and trainable PHY components. | A paper-specific 802.11 waveform/channel model and UHR mechanism; Sionna is not a ready-made Wi-Fi 8 simulator. |
| [GNU Radio](https://github.com/gnuradio/gnuradio) | SDR prototyping for new PHY, sensing, channel-estimation, and reliability ideas. | A compliant waveform/MAC and suitable RF hardware. Existing Wi-Fi out-of-tree modules generally target legacy amendments. |
| [INET Framework](https://github.com/inet-framework/inet) | OMNeT++ packet-network experiments with WLAN, mobility, applications, and deterministic/TSN concepts. | Explicit 802.11bn PHY/MAC changes and validation; stock INET WLAN support is not evidence of UHR compliance. |
| [OpenWrt](https://github.com/openwrt/openwrt), [hostap](https://w1.fi/cgit/hostap/), and [Linux wireless](https://git.kernel.org/pub/scm/linux/kernel/git/wireless/wireless-next.git/) | Real-device testbed and the likely upstream route for standardized features. | Kernel API, driver, firmware, hardware, userspace, and peer-device support for each tested 11bn feature. |
| [OpenWrt `usteer`](https://github.com/openwrt/usteer) | Open client-steering and roaming experiments across multiple APs, relevant to reliability and mobility baselines. | 802.11bn-specific signaling/algorithms; steering alone is not Wi-Fi 8. |
| [DAWN](https://github.com/berlin-open-wireless-lab/DAWN) | Distributed AP steering and network monitoring on OpenWrt, useful as a transparent multi-AP baseline. | Standards-aligned UHR coordination and controlled experimental validation. |

### Broadband platforms relevant to future Wi-Fi 8

No RDK-B, prpl, OpenSync, USP, or EasyMesh project should currently be labelled
a complete open-source Wi-Fi 8 implementation. They are nevertheless relevant
to 802.11bn because UHR behavior has to be configured, coordinated across APs,
observed, and upgraded in deployed operator gateways.

| Project | Potential 802.11bn role | Evidence required before claiming Wi-Fi 8 support |
|---|---|---|
| [RDK-B OneWifi](https://github.com/rdkcentral/OneWifi) and [Wi-Fi HAL interface](https://github.com/rdkcentral/rdkb-halif-wifi) | Represent UHR/MLO capabilities, configure radios and links, collect reliability metrics, and provide a stable contract to chipset implementations. | Merged 11bn-specific APIs and data-model mappings, a conforming platform HAL, driver/firmware support, and interoperability results. Generic radio management is insufficient. |
| [prplOS](https://github.com/prplfoundation/prplOS) and [prplMesh](https://github.com/prplfoundation/prplMesh) | Open gateway/testbed integration and multi-AP control for coordinated reliability, mobility, policy, and telemetry experiments. | A named TGbn mechanism, corresponding hostap/kernel/platform changes, supported hardware, and repeatable multi-node tests. EasyMesh support alone is not 802.11bn support. |
| [OpenSync](https://github.com/plume-design/opensync) | Vendor-neutral orchestration, topology, steering, telemetry, and fleet rollout of future UHR features through target adapters. | Public schema/target changes for a defined 11bn feature plus an underlying open or documented implementation. Cloud policy names alone are not protocol support. |
| [OB-USP-Agent](https://github.com/BroadbandForum/obuspa) with the [Broadband Forum data-model project](https://github.com/BroadbandForum/data-model-template) | Remote configuration and observability when Broadband Forum Wi-Fi data models acquire the necessary UHR/link/reliability objects. | Published data-model objects, implemented component bindings, and end-to-end mapping to real 802.11bn behavior. The USP transport itself provides none of these semantics. |

This distinction matters for broadband source reviews: management frameworks
often gain enums, data-model leaves, or telemetry counters before silicon can
exercise the feature. Those commits are valuable integration progress, but
they should be tagged **Wi-Fi 8 enabling infrastructure**, not **Wi-Fi 8
implementation**.

### Research and standards communities

- **[IEEE 802.11 Task Group bn](https://www.ieee802.org/11/Reports/tgbn_update.htm)** — authoritative public project status and meeting updates for Ultra High Reliability. Use the [IEEE 802.11 document server](https://mentor.ieee.org/802.11/documents) to locate public submissions; availability and reuse terms vary, and documents are not source code.
- **[IEEE 802.11 working group timelines](https://www.ieee802.org/11/Reports/802.11_Timelines.htm)** — a reality check for draft and publication maturity before attaching “Wi-Fi 8” to a project.
- **[Wireless Broadband Alliance](https://wballiance.com/)** — operator/vendor trials and deployment discussions around next-generation Wi-Fi and roaming. This is an industry community, not an open implementation.
- **[Telecom Infra Project Open Converged Wireless](https://telecominfraproject.com/open-converged-wireless/)** — open/disaggregated Wi-Fi infrastructure discussions that can host future UHR work; it is not presently a complete 802.11bn stack.
- **ns-3, Linux wireless, and OpenWrt communities** listed in the Wi-Fi 7 section are the most actionable open venues for writing, reviewing, and deploying code rather than only following the specification.

### A reproducible Wi-Fi 8 research workflow

1. Tie the experiment to a named TGbn mechanism or contribution, not to the
   “Wi-Fi 8” label alone.
2. Begin with a tagged ns-3 release or an exact commit and an EHT/MLO baseline.
3. Publish the patch, topology, channel/error models, traffic model, random
   seeds, and analysis scripts under an open-source licence.
4. Report distribution and tail metrics (for example, 95th/99th percentile
   latency, deadline misses, outage time, and packet delivery), not only mean
   throughput.
5. Compare against unmodified Wi-Fi 7 and disclose every behavior the simulator
   cannot represent. If testing hardware, also record kernel, hostap, driver,
   firmware, regulatory domain, and peer-device versions.

---

## Inclusion and contribution policy

A proposed addition should provide:

1. a canonical source repository (not only a project homepage);
2. an OSI-approved or clearly stated open-source licence;
3. a README, code, tests, examples, or commits showing a concrete 802.11be or
   802.11bn feature;
4. the exact implemented layer and feature; and
5. a candid distinction between simulation, emulation, host driver, firmware,
   userspace control, and a complete stack.

When adding a paper-specific repository, link both the code and paper, record
the last reproducible revision, and say whether the upstream simulator already
contains the work. This keeps the catalogue useful and prevents abandoned
one-off forks or Wi-Fi 6 models with changed data rates from being mistaken for
Wi-Fi 7/8 implementations.
