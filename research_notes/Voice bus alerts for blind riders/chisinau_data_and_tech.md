# Chișinău real-time transit data and technical approaches for a voice "which bus is arriving" app

Research note, 2026-11-11. Method caveat: this session's network blocked direct fetches of ipn.md, moldpres.md and tranzy.ai, and GitHub API access to the roataway repos. Some findings therefore rest on search-engine snippets of those pages, not on the full text, and are marked "(snippet)". GitHub pages for roataway were fetched directly. No live test of the MQTT broker was possible.

## 1. What real-time vehicle location data exists for Chișinău, and how can it be accessed?

### Takeaway
Real-time GPS data for Chișinău exists and has been opened up in two ways. One is the community project Roataway: an MQTT feed run by Dekart (opendata.dekart.com), with ~3 s updates, route number, board number, heading and speed. The other is Tranzy.ai's token-based OpenData API, which lists "RTEC&PUA Chisinau" as an agency. Coverage is incomplete: not every vehicle has a tracker, and I found no evidence that private minibuses (rutiere) are tracked. There is no official GTFS / GTFS-Realtime feed from the city. Whether either feed is still live in late 2026 is unverified.

### Cited Findings
**Roataway / Dekart MQTT feed (community open data)**
- The feed is MQTT with JSON payloads. The broker is `opendata.dekart.com`, port 1945 (MQTT) and 1946 (MQTT over websockets) — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- Topics: `telemetry/transport/+` (per tracker ID), `telemetry/route/+` (grouped by route upstream ID) and `event/route/+` (a "remove" event when a vehicle leaves a route) — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- Payload fields: latitude, longitude, direction (float), speed (km/h), UTC timestamp. Route topics also carry `board` (fleet/board number), `rtu_id` (tracker) and `route` (route name) — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- "Vehicles send telemetry every ~3s" — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- The docs credit Dekart for "designing a system based on non-proprietary protocols and for opening it up to the public". No licence is stated, and the only vehicle example is a trolleybus (board 1273) — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- `infrastructure-data` holds routes.csv (with OSM relation IDs), vehicles.csv (tracker ID, board number, model, doors, articulated and accessibility flags) and per-route stations/segments JSON. It states that "not all trolleybuses have GPS trackers yet" — [roataway/infrastructure-data](https://github.com/roataway/infrastructure-data)
- A REST wrapper returns the last known trolleybus locations and is fed by MQTT — [roataway/rest-api](https://github.com/roataway/rest-api) (snippet)
- A community static GTFS exists ("Gtfs static infomation for Chisinau city"). It has GTFS_static and stations_routes folders, 12 commits, 4 open issues, and no licence or update date visible — [roataway/gtfs-data](https://github.com/roataway/gtfs-data)

**Tranzy.ai (Romanian company)**
- Tranzy runs an open data portal with real-time vehicle, station and route data. It works with operators and authorities in Cluj-Napoca, Iași, Botoșani and Chișinău — [Tranzy open data](https://tranzy.ai/opendata/) (snippet); [Tranzy startup presentation, Interoperable Europe](https://interoperable-europe.ec.europa.eu/sites/default/files/document/2024-12/Tranzy.ai_startup_presentation.pdf)
- The API (`api.tranzy.dev/v1/opendata`) "partially implements the GTFS specification". It is labelled beta, needs a token tied to an account and an application per agency, and lists the agency "RTEC&PUA Chisinau" — [horace42/tranzy-stats (third-party)](https://github.com/horace42/tranzy-stats) (snippet); beta portal [opendata-beta.tranzy.ai](https://opendata-beta.tranzy.ai/)
- The free Tranzy app (Android/iOS) shows when trolleybuses and buses reach stops — [IPN](https://www.old.ipn.md/en/last-adjustments-to-application-showing-when-trolleybuses-and-buses-get-7967_1095603.html) (snippet); [App Store Tranzy](https://apps.apple.com/md/app/tranzy/id1492365417)
- The csuki multi-city app says its real-time data for Cluj-Napoca, Iași, Constanța and Chișinău comes from the Tranzy open data platform — [csuki listing](https://mwm.ai/apps/csuki/6759547842) (snippet)
- Another app, "Chisinau Public Transport Map", advertises a live map and live arrivals per stop. Its data source is unknown — [App Store](https://apps.apple.com/us/app/-/id6760182167)

**Official/municipal side**
- In March 2023 Moldpres reported that a mobile app showing public transport arrival times had become functional, viewable via operators' sites, chisinau.md and mobilitatechisinau.md — [Moldpres 2023-03-15](https://www.moldpres.md/en/news/2023/03/15/23002070) (snippet)
- RTEC tender documents require GPS/AVL data streamed in real time as JSON via REST POST to an RTEC-provided endpoint — [MTender doc](https://storage.mtender.gov.md/get/99b21cf5-34a2-44d5-af2f-40493f314d11-1639164725396) (snippet)
- A November 2024 tender covered connecting to a GPS monitoring (traffic control) program, with a contract to 30 Dec 2025 — [achizitii.md](https://achizitii.md/en/public/tender/21311663/lot/11934592) (snippet)
- An earlier IPN article notes that a GPS-tracking contract expired 31 July 2022, which signals contract churn — [IPN](https://ipn.md/en/platform-enabling-to-monitor-each-trolleybus-in-chisinau-in-real-7967_1089295.html) (snippet)
- The EU-funded "MOVE IT like Lublin" project set up a Traffic Monitoring Center (launched Nov 2024; first smart sensors June 2025) — [EU4Moldova](https://eu4moldova.eu/en/first-smart-traffic-sensors-installed-by-the-traffic-monitoring-center/); [EU project page](https://eu4georgia.eu/projects/eu-project-page/?id=1482)
- A 2025 ASEM conference paper describes 10 pilot "smart stations" that estimate ETA from vehicle GPS, route maps and average speed, with data refreshing "every 30–60 seconds" — [ASEM 2025 paper](https://irek.ase.md/jspui/bitstream/123456789/4097/1/ASEM_Creating%20the%20Society%20of%20Consciousness_TELE%20%e2%80%93%202025_p35-39.pdf) (snippet). Some passengers interviewed said the stop displays need improvement (same search context; source attribution uncertain).
- PUA (Parcul Urban de Autobuze) runs 40+ urban and suburban routes — [autourban.md](https://autourban.md/en). Municipal bus count rose from 85 to 145 in four years — [Albasat](https://albasat.md/en/number-of-buses-on-municipal-routes-rose-from-85-to-145-in-four-years)
- Static GTFS in MobilityDatabase was found only for Ungheni (mdb-3046), not Chișinău — [MobilityDatabase](https://mobilitydatabase.org/feeds/gtfs/mdb-3046)

**E-ticketing / validators (a possible secondary signal)**
- The Municipal Council approved a concept for a unified electronic payment and validation system for buses and trolleybuses (RTEC + PUA, >650 vehicles), with cash retained — [Logos Press](https://logos-pres.md/en/news/the-capital-has-approved-the-introduction-of-electronic-tickets/) (snippet)
- maib, Mastercard, Paynet, RTEC, PUA and City Hall offer card, phone and smartwatch tap payment at stationary validators on trolleybus routes 3, 22, 23, 24 and 30 (page undated) — [maib](https://maib.md/en/noutati/achita-calatoria-cu-transportul-public-la-validatoarele-stationare-de-pe-mai-multe-rute-de-troleibuze-si-autobuze-din-capitala); [maib transport](https://maib.md/en/transport-public)
- The pilot started with 110 vehicles — [Chișinău City Hall](https://new.chisinau.md/en/the-implementation-of-the-pilot-project-regarding-the-electronic-payment-20292_245152.html) (snippet). The city aimed to finish e-ticketing by 2025 — [IPN](https://ipn.md/en/print/chisinau-municipality-plans-to-finalize-electronic-ticketing-by-2025--7967_1103151.html) (snippet)
- Articulated trolleybuses were delivered with "state-of-the-art information systems" — [IPN](https://ipn.md/en/state-of-the-art-information-systems-in-articulated-trolleybuses-in-7967_1102415.html) (snippet; details not read)

**Third-party navigation apps**
- Moovit's generic marketing says it uses real-time GPS from vehicles where available — [Moovit features](https://moovit.com/features/). Chișinău-specific live data is unconfirmed. Only a secondary nomad guide says Moovit and Google Maps "provide real-time transit directions" there — [Digital Nomad Exchange](https://digitalnomadexchange.com/article/33684/Living-in-Chisinau-Public-Transportation-in-Chisinau)

### Inferences
- Roataway's per-route MQTT topic gives route number, board number and heading at ~3 s. That is enough in principle to say "trolleybus 22, board 1273, ~150 m away, heading toward your stop". This feed is technically the best fit for an MVP, if it is still running.
- Dekart appears to be the AVL vendor behind RTEC's trackers. Its feed and the official/Tranzy feeds probably share the same source data, so if RTEC changes vendor (contracts churned in 2022 and 2025), the community feed could disappear. A formal data agreement with RTEC, PUA and Primăria is therefore a dependency, not a nice-to-have.
- Direction of travel must be inferred. Payloads carry a compass heading but no trip or direction_id, so the app needs map matching against route segments (from infrastructure-data or the GTFS shapes) to tell which side of the street, and which terminus, a vehicle is heading toward.
- Microbuses (rutiere) are private and are the likeliest coverage gap. A blind rider would get no alert for them.

### Gaps
- I could not confirm that `opendata.dekart.com` is still publishing in 2026 (no live connection was possible), or what share of RTEC/PUA vehicles have trackers. I also could not count vehicles.csv rows or find commit dates, because GitHub API access was blocked.
- Tranzy API terms (cost, rate limits, licence, whether redistributing to a third-party app is allowed) are unverified because tranzy.ai was unreachable.
- No source found on date.gov.md publishing transport GPS/GTFS. No official GTFS-RT exists.
- No source on rutiere GPS tracking or open data.
- Wi-Fi/GPS hardware on new trolleybuses: only headline-level evidence.
- Google Maps live transit in Chișinău: unconfirmed.

## 2. Data quality: update frequency, GPS accuracy, direction and route

### Takeaway
The vehicle-side feed is fast (~3 s per Roataway docs). The biggest uncertainty is the user's phone: urban smartphone GNSS error ranges from under 5 m RMS on good phones to 50–100 m (95%) on older devices in dense downtowns. That is too coarse to separate two vehicles at the same stop, or to tell which side of the road a stop is on, by GPS alone.

### Cited Findings
- Feed telemetry interval is ~3 s, with heading and speed included — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- Municipal smart-station ETAs refresh about every 30–60 s — [ASEM 2025](https://irek.ase.md/jspui/bitstream/123456789/4097/1/ASEM_Creating%20the%20Society%20of%20Consciousness_TELE%20%e2%80%93%202025_p35-39.pdf) (snippet)
- DXOMARK/Geoflex tests across urban, dense-urban and open-sky settings: best phones averaged <5 m horizontal RMS error, weaker ones ~8 m — [DXOMARK](https://www.dxomark.com/gps-on-your-smartphone-why-youre-not-always-there-when-it-says-youre-there/)
- Downtown pedestrian tests with mass-market GPS phones gave a 95% CEP of 50–100 m — [Fierce Network](https://www.fierce-network.com/europe/navigate-city) (older article)
- A 2026 arXiv study found environment, not hardware, is the main driver of smartphone GNSS error, and dense urban and indoor settings consistently degrade it — [arXiv 2603.26706](https://arxiv.org/html/2603.26706v1)
- An InsideGNSS review of smartphone GNSS performance — [InsideGNSS](https://insidegnss.com/what-are-the-actual-performances-of-gnss-positioning-using-smartphone-technology/)

### Inferences
- End-to-end latency is roughly 3 s of telemetry interval, plus network and broker delay, plus app processing, so about 5–10 s. At an urban approach speed of 20–30 km/h (~6–8 m/s), the vehicle moves ~30–80 m in that time. "Arriving now" alerts should therefore be triggered predictively (for example at 150–300 m, or ETA under 60 s), not on exact arrival. This is an estimate, not measured.
- The vehicle trackers are fixed, roof-mounted units and are probably more accurate than phones, but no accuracy spec was found.
- Two vehicles at once: the feed can tell them apart by route and board number. Which one is physically at the door, and in which bay, cannot be resolved reliably with 5–50 m error at both ends. The app should announce the order ("22 first, then 3 about 40 m behind") and not claim certainty.

### Gaps
- No published accuracy spec for the RTEC/PUA trackers.
- No measured end-to-end latency for the Dekart or Tranzy feeds.
- No Chișinău-specific smartphone GNSS study.

## 3. Comparing technical approaches (GPS geofence, camera OCR, BLE beacons, hybrid)

### Takeaway
GPS feed plus geofence/ETA is the only approach that needs no hardware and no camera aiming. It can launch now if feed access holds, but it cannot resolve "which vehicle is at the door". Camera recognition of route numbers reaches ~95% on small research datasets, but it requires the user to aim a phone, which is a heavy ask for blind users. BLE beacons on vehicles have Romanian precedents (Bucharest 2015, Sibiu 2015), detect at about 50–60 m, and cost ~€10–15 per beacon in hardware. They need operator cooperation to install and maintain. A hybrid (GPS for ETA, BLE or camera for final confirmation) is the most accurate design.

### Cited Findings
**Camera OCR / detection**
- A YOLO detector with OCR, super-resolution and Levenshtein correction against known route lists reached 95% accuracy on 120 hard images (low light, reflections, motion blur) — [Crossref record, Algorithms 18(10):616](https://api.crossref.org/works/10.3390%2FA18100616) (snippet)
- A comparison of Faster R-CNN, YOLOv4 and YOLOv5s for bus number recognition found YOLOv5s best for accuracy and speed — [KoreaScience 2022](https://koreascience.kr/article/CFKO202232558475350.page) (snippet; no figures)
- A SIFT+OCR classical pipeline reached 87.63% (decision tree), 83.84% (RF) and 83.75% (SVM) — [Springer chapter 2023](https://link.springer.com/chapter/10.1007/978-981-99-3656-4_8) (snippet)
- An on-device pipeline running three deep models on one phone, offline, for bus line recognition — [ICT Innovations, embedded DL bus line recognition](https://proceedings.ictinnovations.org/attachment/paper/559/embedded-deep-learning-to-aid-the-mobility-of-individuals-with-disabilities-a-solution-for-in-house-bus-line-recognition.pdf) (snippet; accuracy not seen)
- A wearable assistive device using transfer learning — [Cardiff Met](https://pure.cardiffmet.ac.uk/en/publications/a-wearable-assistive-device-for-safe-travel-using-transfer-learni/)

**BLE beacons**
- Bucharest (Onyx Beacon, May 2015): 40 beacons on 40 buses, later 500 of 900 buses. The phone alerts when the requested bus is within ~50–60 m of the stop — [RFID Journal](https://www.rfidjournal.com/?p=72206); [MediaPost](https://www.mediapost.com/publications/article/265283/beacons-on-buses-aid-visually-impaired-riders.html); [Mobile Marketing Magazine](https://mobilemarketingmagazine.com/?p=93417)
- Sibiu (SASA, Nov 2015): beacons on all 160 buses and 840 stops, and any passenger can get arrival alerts — [RFID Journal](https://www.rfidjournal.com/?p=72250)
- Austin (Capital Metro): beacons at 16 downtown stops, integrated with BlindSquare, so no custom app was needed — [StateScoop](https://statescoop.com/?p=8312)
- A Brazilian research BLE system with on-bus hardware and a phone app that guides users to approaching buses — [SBRC paper](https://sol.sbc.org.br/index.php/sbrc/article/download/7374/7258)
- Hardware pricing: Minew MWB01 IP67 iBeacon at $13 each (1 unit) or $11 (100 units), €11.23 in EUR. Minew MWC02 IP67 card at €11.90, or €10.20 at 100 units. Distributor list prices ex-VAT — [DigiKey MWB01](https://azcus.digikey.com/en/products/detail/minew/MWB01/26724088); [DigiKey BG](https://www.digikey.bg/en/products/detail/minew/MWB01/26724088); [DigiKey MWC02](https://www.digikey.be/en/products/detail/minew/MWC02/26724087)

**Standards**
- GTFS-Realtime (VehiclePositions, TripUpdates) is the standard format a city feed could adopt — [gtfs.org realtime reference](https://gtfs.org/documentation/realtime/reference/)

### Inferences
Rough comparison (author's synthesis, not measured):

| Approach | Accuracy at the door | User effort | Infra cost | Dependency |
|---|---|---|---|---|
| GPS feed + phone location + ETA | Good for "route X ~1 min away". Poor for "this one at the door" (5–50 m phone error, 5–10 s latency) | None (background) | ~0 hardware | Feed access (RTEC/Dekart/Tranzy) |
| Camera OCR | ~84–95% in papers on small datasets. Likely worse at night, in rain and at distance | High: must aim the camera, short range | 0 | None; battery drain from continuous inference |
| BLE beacon on vehicle | ~50–60 m detection, identifies a specific vehicle | Low | ~€10–15 per beacon plus install and batteries; 650+ vehicles ≈ €7–10k hardware | Operator install and maintenance |
| Hybrid (GPS ETA + BLE/camera confirm) | Best | Low–medium | Medium | Both |

- Fleet beacon hardware estimate: 650 vehicles × ~€11 ≈ €7.2k, excluding installation, mounting, replacement batteries and fleet management software. This is an inference from the price lists above.
- Camera OCR is best as an optional "confirm" mode, not the primary trigger.
- Two vehicles at once: BLE RSSI can roughly rank proximity, but bodies and metal make it noisy. Exact door position remains unsolved by any single method.

### Gaps
- No night/rain-specific accuracy, recognition distance or battery data from the OCR papers (full texts not read).
- No independent evaluation of the Bucharest or Sibiu beacon results or their usage numbers. Current status unknown.
- Beacon install labour cost in Moldova not found. Kontakt.io pricing not found.

## 4. Text-to-speech (Romanian/Russian), haptics, smartwatch

### Takeaway
Apple's iOS 26 feature-availability pages list both Romanian and Russian for VoiceOver/Spoken Content. On Android, Romanian depends on the TTS engine: Google's engine or third-party engines are needed, and some Samsung engines lack Romanian. Russian is widely available. The apps can also speak through the platform TTS APIs directly.

### Cited Findings
- iOS 26 feature-availability pages list Romanian and Russian under VoiceOver, Live Speech and Spoken Content — [Apple iOS feature availability (UK)](https://apple.com/uk/ios/feature-availability/); [Apple (BY)](https://www.apple.com/by/ios/feature-availability)
- Apple notes that some International-setting languages have no matching VoiceOver voice — [Apple HT3562 (archived)](https://support.apple.com/kb/HT3562)
- AppleVis users report the Romanian VoiceOver voice "Ioana" — [AppleVis forum](https://www.applevis.com/forum/ios-ipados/question-about-installing-voiceover-voices-other-languages)
- Android: TalkBack reads through the active TTS engine. Some Samsung phones lack Romanian in the default Samsung TTS. A Piper-based third-party "Romanian TTS – Lili" exists — [Lili Romanian TTS](https://eduardm.codeberg.page/romanian-tts-lili-android/). A Samsung Romanian voice package also exists — [APKMirror SamsungTTS Romanian](https://apkmirror.com/apk/samsung-electronics-co-ltd/samsungtts-romanian-female/samsungtts-romanian-female-302211081-release)
- watchOS has a feature-availability list per language — [Apple watchOS feature availability](https://www.apple.com/ua/watchos/feature-availability/)
- Card, phone and smartwatch payment already works at Chișinău validators, a precedent for watch use in transit — [maib](https://maib.md/en/transport-public)

### Inferences
- Haptics (Core Haptics on iOS, the Android Vibrator API) and watch notifications are standard platform features. Distinct vibration patterns per route are feasible, but I did not research them with sources here.
- Ship a bilingual (RO/RU) announcement mode that follows the system TTS. Detect a missing Romanian voice on Android and prompt the user to install Google TTS voice data.

### Gaps
- Not verified: whether Google's Speech Services Romanian voice is installed by default on Moldovan-market phones.
- No sourced information on Wear OS Romanian TTS.

## 5. MVP cost/effort, ongoing cost, legal and data access

### Takeaway
I found no direct sources on cost benchmarks. The estimates below are the author's inferences. Legally, the key issue is the unclear licence and continuity of the Dekart/Roataway feed and the Tranzy API. A formal agreement with Primăria / RTEC / PUA, or with Tranzy, should precede a public launch.

### Cited Findings
- The Roataway feed docs give no licence. Dekart is credited with opening the data — [roataway/api-documentation](https://github.com/roataway/api-documentation)
- The Tranzy API needs per-agency tokens and is in beta "subject to change" — [horace42/tranzy-stats](https://github.com/horace42/tranzy-stats) (snippet)
- The official vehicle-to-server feed goes to an RTEC-provided endpoint, so RTEC controls the data — [MTender doc](https://storage.mtender.gov.md/get/99b21cf5-34a2-44d5-af2f-40493f314d11-1639164725396) (snippet)
- Tranzy already partners with the Chișinău authorities and is a likely commercial data partner — [Tranzy presentation](https://interoperable-europe.ec.europa.eu/sites/default/files/document/2024-12/Tranzy.ai_startup_presentation.pdf)

### Inferences (unsourced estimates — treat as rough)
- MVP (GPS feed only, iOS and Android, RO/RU TTS, nearest-stop detection, route filter, watch notification): about 2–4 people (1–2 mobile, 1 backend/data, plus a part-time UX/accessibility tester with blind users) for about 3–5 months.
- Ongoing: a small backend that subscribes to MQTT and fans out push notifications, likely tens of € per month at small scale, plus maintenance and app-store fees. Beacons, if added, cost about €7–10k in hardware for the RTEC+PUA fleet, plus installation.
- Risk: the feed vendor or contract changes (2022 and 2025 contract cycles). Mitigation: abstract the data source so the app can run on Dekart MQTT, Tranzy or a future official GTFS-RT.

### Gaps
- No Moldovan data-protection or open-data law analysis was done. Vehicle GPS is likely not personal data, but user location is.
- No sources on salary or cost benchmarks for Moldovan developers.
- Unknown whether Primăria has an open-data policy covering AVL data.
