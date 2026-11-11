# Existing solutions telling blind/low-vision riders which bus is approaching

Method note: the evidence comes from about 20 web searches, read through search-result snippets. Direct page fetches failed because some hosts (navilens.com, api.crossref.org) are outside this session's network allowlist. As a result, several figures below are snippet-level vendor claims and should be checked on the primary page before they go into the final report. Today's date is 2026-11-11.

## 1. Mainstream transit apps: accessibility and Moldova/Romania coverage

### Takeaway
Moovit is the only mainstream app with documented blind-specific features: full VoiceOver/TalkBack support, audio-condensed directions, Get Off alerts, and a WeWALK smart-cane integration. Moovit and Google Maps both appear to carry Chișinău route data. In Chișinău, real-time arrival data comes mainly from the city/Tranzy platform launched in March 2023, and that platform has incomplete GPS coverage and no declared accessibility features. None of these apps tells a rider at the stop which vehicle is physically arriving at that moment. They give ETAs only.

### Cited Findings
- Moovit v4.10 (2016) made every screen accessible with VoiceOver (iOS) and TalkBack (Android). It was developed with a blind developer, Adi Kushnir — [TechCrunch 2016](https://techcrunch.com/2016/03/30/moovit-expands-accessibility-features-for-blind-users); [News-Medical](https://www.news-medical.net/news/20160401/Latest-version-of-Moovit-app-now-includes-new-features-for-blind-community.aspx); [Moovit update blog](https://updates.moovit.com/accessibility-features-blind-users-one-year/)
- Moovit condenses route, trip duration and entry/exit stops into clear spoken sentences — [Moovit blog, World Sight Day 2019](https://moovit.com/blog/world-sight-day-2019-discover-moovit-accessibility-features/)
- Moovit and WeWALK partnership (Dec 2021): the WeWALK smart cane app uses Moovit's Transit API. Users can find stops, hear when a bus is due, and get Get Off Alerts and step-by-step live directions, including on multimodal routes. Voice runs through the cane's Bluetooth link — [Moovit press release](https://moovit.com/press-releases/moovit-and-wewalk-announce-partnership-to-enhance-mobility-of-visually-impaired-people/); [Moovit blog](https://moovit.com/blog/moovit-wewalk-partnership); [TechCrunch 2021](https://techcrunch.com/2021/12/02/moovit-partners-with-smart-cane-company-wewalk-to-enhance-mobility-for-visually-impaired/)
- Moldova's official tourism booking site recommends Moovit and map.md for Chișinău routes, which cover trolleybuses, buses and minibuses — [booking.moldova.travel help](https://booking.moldova.travel/help)
- Moldova's tourism site says Chișinău's network and schedules are "quite accurate" in Google Maps — [moldova.travel Getting Around](https://moldova.travel/en/?p=20948)
- Chișinău's urban mobility platform and traveler app went live on 15 March 2023. More than 400 trolleybuses and 210 buses had tracking software at launch — [Moldpres 2023](https://www.moldpres.md/en/news/2023/03/15/23002070)
- Tranzy (TRANZY AI S.R.L., Romania) offers real-time tracking in Chișinău. Business Review (2023) reports more than 150,000 daily users across Iași, Cluj-Napoca, Botoșani and Chișinău, and describes an audio accessibility feature for visually impaired people as "soon to be launched". No later confirmation that it launched was found — [Business Review / Issuu 2023](https://issuu.com/businessreview/docs/br_04_2023/s/24634366); [IPN](https://www.old.ipn.md/en/last-adjustments-to-application-showing-when-trolleybuses-and-buses-get-7967_1095603.html)
- Tranzy's App Store listing says the developer has not indicated which accessibility features the app supports. User reviews say not all vehicles are GPS-connected — [App Store MD](https://apps.apple.com/md/app/tranzy/id1492365417)
- Tranzy runs an open-data portal for real-time vehicle data — [tranzy.ai](https://tranzy.ai/)
- More than 150 digital information panels are planned for Chișinău waiting stations — [IPN](https://ipn.md/en/over-150-digital-information-panels-to-be-installed-at-waiting-7967_1103150.html)
- The Transit app covers Bucharest (STB) — [transitapp.com region page](https://transitapp.com/en/region/bucurești/stb-societatea-de-transport-bucurești). On AppleVis, users rate Transit as accessible, but anecdotal reports say the BlindSquare developer could not get the two apps to interoperate — [AppleVis Transit](https://www.applevis.com/apps/ios/navigation/transit-subway-bus-times)
- Yandex Maps tracks public transport in real time in general — [Yandex support](https://yandex.com/support/maps/en/concept/public-transport). No Chișinău-specific confirmation was found.

### Inferences
- An integration path exists. Real-time vehicle positions for Chișinău are available from Tranzy open data and from the roataway MQTT/GTFS feeds (section 5). A voice layer could be built on these feeds instead of on Moovit or Google.
- Every mainstream app depends on GPS/ETA. None can say "the vehicle in front of you is route 22" with certainty, which is the core gap for a blind rider at a busy stop.

### Gaps
- No source confirms whether Moovit or Google Maps carry *real-time* (rather than scheduled) data for Chișinău.
- No coverage of Citymapper was found for Moldova or Romania. Citymapper is not known to operate there, but that is unconfirmed.
- Moovit's Romanian/Russian voice output depends on the OS TTS engine. No source documents it.

## 2. Blind-specific tools (NaviLens, BlindSquare, Lazarillo, AI vision apps)

### Takeaway
NaviLens is the most widely deployed blind-specific bus solution. Its codes on stops (and sometimes vehicles) are read from 14–30+ m, and the codes speak lines and next arrivals. It is concentrated in Spain, France and North America, and no Moldova or Romania deployment was found. General AI vision apps (Be My AI, Seeing AI, Lookout, Envision) can read text but are not designed for approaching buses at distance. Navigation apps (BlindSquare, Lazarillo) get users to the stop and pull in transit arrivals where an API exists.

### Cited Findings
- **NaviLens detection range (vendor claims):** about 30 m with no aiming in Bordeaux — [NaviLens Bordeaux](https://www.navilens.com/en/case-studies/bordeaux-tbm-bruges); 30 m in Austin — [CapMetro](https://www.navilens.com/en/case-studies/capmetro-austin); 50 ft in San Antonio — [VIA](https://www.navilens.com/en/case-studies/via-san-antonio); 14 m in Vancouver — [TransLink](https://www.navilens.com/en/case-studies/translink-vancouver); "+30 m, up to 160° angle" and "100 ft" on vendor sector pages — [NaviLens buses](https://www.navilens.com/en/sectors/transport/buses); [NaviLens transport](https://www.navilens.com/en/sectors/transport)
- The NYC MTA piloted NaviLens on Bronx bus stops. Coverage at the time said codes were detectable from about 40 ft — [MTA press release](https://www.mta.info/press-release/mta-pilots-smartphone-app-to-help-blind-and-low-vision-bus-riders); [ITS International](https://d9prod.itsinternational.com/its17/news/new-york-helps-blind-riders-find-buses); [NaviLens MTA case](https://www.navilens.com/en/case-studies/mta-bronx-stops)
- Madrid EMT: all 5,540 shelters and stops were tagged by May 2023. The NaviLens GO app is free and needs no electronics. The vendor says the codes speak next arrivals, lines and incidents in 42 languages — [NaviLens EMT Madrid](https://www.navilens.com/en/case-studies/emt-madrid)
- Barcelona TMB: about 40,000 monthly reads across bus and metro in 2025. The codes can be read in the NaviLens and NaviLens GO apps or in the TMB App — [Catalan News](https://www.catalannews.com/society-science/item/navilens-system-revolutionizes-public-transport-for-visually-impaired-with-40000-reads-monthly); [TMB](https://transparencia.tmb.cat/en/web/tmb/conoce-tmb/mejoras-red-transporte/otras-mejoras/etiquetas-inteligentes-navilens)
- Other NaviLens transit deployments: Alicante, Las Palmas, Vancouver — [Vectalia](https://www.navilens.com/en/case-studies/vectalia-alicante); [Guaguas](https://www.navilens.com/en/case-studies/guaguas-las-palmas)
- **Be My AI** (GPT-4-based) lists reading the bus number at a stop as an intended use case. This is vendor material, and no accuracy-at-distance data was found — [Be My Eyes announcement](https://bemyeyes.com/blog/announcing-be-my-ai)
- **Seeing AI** reads short text and documents aloud from the camera, with no bus-specific mode — [Guide Dogs UK](https://www.guidedogs.org.uk/getting-support/information-and-advice/how-can-technology-help-me/tech-selector/seeing-ai). **Google Lookout** has Explore, Shopping and Quick Read modes and no bus mode — [Google blog](https://www.blog.google/outreach-initiatives/accessibility/lookout-discover-your-surroundings-help-ai/)
- A 2020 student report argued that Seeing AI and Lookout are "very generic and are not tailored to use in identification of bus numbers, especially at a large distance" — [Tang, bus.pdf](https://brandontang89.github.io/static_files/reports/bus.pdf)
- **Lazarillo** integrates transit APIs to provide stop locations and bus arrival/departure times, for example with DDOT/SMART in Detroit — [Lazarillo](https://lazarillo.app/?p=25668)
- **BlindSquare** plus Connecthings beacons were piloted at 16 bus stops in Austin (2018) — [GovTech](https://www.govtech.com/transportation/Technology-Guides-Blind-Transit-Riders-Right-to-the-Bus-Door.html)
- **OKO (AYES, Antwerp)** is a free AI app that recognises pedestrian traffic lights and gives audio/haptic walk/don't-walk cues. It is iPhone-first and not a bus tool — [Innovation Origins](https://innovationorigins.com/en/free-ticker-app-oko-from-ayes-allows-the-blind-and-visually-impaired-to-cross-roads-safely-again); [AppleVis](https://applevis.com/comment/148150)
- **Flowy** (Korea) lets a visually impaired user photograph an approaching bus to identify it — [jotup.co](https://jotup.co/node/1575575) (secondary source, weak)

### Inferences
- NaviLens tells users about arrivals *at the stop*. It is not designed to identify which moving vehicle is pulling in, unless codes are also placed on vehicle doors or sides, and the deployment of vehicle codes is unverified.
- Deploying NaviLens in Chișinău would require an agency contract and code installation, plus an integration of NaviLens with the real-time feed.
- General AI apps need the user to point a camera at a moving bus. That is a usability barrier, and no published accuracy numbers exist for this use.

### Gaps
- No pricing for agency licensing of NaviLens was found. Whether NaviLens supports Romanian and Russian could not be confirmed because the vendor site was not fetchable.
- No sources were found on Envision/Ally or Aira for bus identification, or on Wayfindr-standard bus deployments.

## 3. Hardware and infrastructure systems (beacons, driver alerts, external speakers, REACT)

### Takeaway
Bluetooth beacon systems on buses have a long track record: Warsaw 2014, Bucharest RATB 2015 (Onyx Beacon, about 70 m range), a US Tandem pilot, and Bucharest's STEP HEAR on STB lines 90 and 104 in 2025. These systems announce the chosen route as the bus approaches and often alert the driver. Driver-alert "bus stop bell" systems exist in Korea. External route announcements are mandated in the US but no EU mandate was found. UK REACT talking signs are push or fob-triggered.

### Cited Findings
- **Bucharest, Onyx Beacon / RATB (2015):** 500 iBeacons were fitted on Bucharest buses and trolleybuses by Cluj firm Onyx Beacon. The user selects a line in the app, and when that line's bus comes within about 60–70 m the phone announces it via VoiceOver. Battery life is up to 4 years — [Playtech.ro](https://playtech.ro/2015/clujenii-ofera-solutia-de-siguranta-pentru-nevazatori-in-transportul-public/); [StartupCafe](https://www.startupcafe.ro/idei-si-antreprenori/beacon-smart-shopping-microlocalizare.htm). Current status is unknown.
- **Onyx Beacon + Tandem (US pilot):** 100 visually impaired riders took part, and the bus beacon sounded a tone to alert the driver when the rider was within about 10–20 m — [RFID Journal](https://www.rfidjournal.com/?p=72206); [Mobile Marketing Magazine](https://mobilemarketingmagazine.com/?p=93417)
- **Bucharest STB + STEP HEAR (2025):** the app gives audio guidance to the stop, vehicle arrival information and boarding/alighting sound assistance, and drivers are notified in advance. It started on lines 90 and 104 and was developed with Asociația Nevăzătorilor din România. STB expects about 6,000 beneficiaries — [Romania Insider 2025](https://www.romania-insider.com/bucharest-public-transport-app-visually-impaired-2025); [Spotmedia](https://spotmedia.ro/stiri/social/bucuresti-sistem-inteligent-pentru-sprijinirea-nevazatorilor-in-transportul-public); [G4Media](https://www.g4media.ro/stelian-bujduveanu-incepand-de-astazi-primaria-capitalei-prin-stb-pune-in-aplicare-un-proiect-important-dedicat-persoanelor-cu-deficiente-de-vedere-un-sistem-inteligent-care-ofera-mai-multa-indep.html)
- **Virtual Warsaw (2014):** an Ifinity pilot put beacons on 30 buses on one line. The app could announce the stop, vibrate, and notify the driver. It was funded by the Bloomberg Mayors Challenge, about €1M. Sources conflict on the planned citywide scale (400,000 vs. 1 million beacons) — [OECD OPSI](https://oecd-opsi.org/wp-content/uploads/2019/07/Virtual-Warsaw_Poland_2014.pdf); [Bloomberg Cities](https://bloombergcities.jhu.edu/news/how-partnership-visually-impaired-helped-virtual-warsaw); [EU-Startups](https://www.eu-startups.com/2014/09/biggest-beacons-implementation-comes-to-warsaw-implemented-by-ifinity/). Current status is unknown. A successor app, YourWay, exists — [App Store](https://apps.apple.com/fi/app/yourway/id1473003545)
- **Gyeonggi "Bus Stop Bell" (Korea):** in the G-Bus app, the rider requests a stop and a device on the driver's seat sounds an alert. The request works only within 30 m of the stop and when the bus is at least one stop away — [Gyeonggi Province](https://english.gg.go.kr/?p=17046)
- A Korean high-school prototype pairs a rider app with a driver app and uses beacons to announce bus numbers. It reported 92% effectiveness across 13 users — [Society for Science abstract](https://abstracts.societyforscience.org/Home/PrintPdf/24618)
- **US ADA guidelines** require external audible route identification at boarding areas — [CORADA T704](https://corada.com/documents/part-1192-ada-guidelines-transportation-vehicles-preamble/t704-announcement-systems); [eCFR 1192.61](https://www.ecfr.gov/current/title-36/chapter-XI/part-1192/subpart-C/section-1192.61). Toronto's TTC piloted external route announcements — [Metro Magazine](https://www.metro-magazine.com/news/toronto-transit-set-to-test-talking-buses)
- **UK Accessible Information Regulations** require audible and visible on-board stop information. No external-speaker requirement was found — [GOV.UK](https://www.gov.uk/government/publications/providing-accessible-information-onboard-local-bus-and-coach-services/providing-accessible-information-onboard-local-bus-and-coach-services)
- **RNIB REACT** talking signs are triggered by a key fob, app or push button and can speak real-time display info. RNIB no longer sells the fobs. Portsmouth installed 90 talking bus stops — [RNIB help](https://help.rnib.org.uk/help/daily-living/react-talking-sign); [Sight Advice FAQ](https://www.sightadvicefaq.org.uk/independent-living/transport-travel/react-talking-sign); [React Access Trigger app](https://apps.apple.com/gb/app/react-access-trigger/id1591749718)

### Inferences
- Beacon-on-bus is the only proven way to identify *which physical vehicle* is arriving, at about 10–70 m. Its cost is per-vehicle hardware plus maintenance, and several pilots (Warsaw, RATB 2015) seem to have faded. Bucharest STB is a live regional precedent worth contacting, and so is Asociația Nevăzătorilor din România.
- Chișinău vehicles already carry GPS trackers. GPS proximity to the stop could approximate the beacon function, but accuracy at crowded multi-route stops would be weaker.

### Gaps
- No EU-level mandate for external route announcements was found. UNECE R107 and the European Accessibility Act were not checked.
- No sources were found on Japanese systems, Totupoint, "BusTalk", or Romanian smart-city beacon projects in Cluj, Iași or Timișoara.
- Per-unit costs for the beacon systems were not found.

## 4. Research prototypes: camera-based bus number recognition

### Takeaway
Academic systems that detect the bus (with YOLO), then run OCR and announce the result by TTS, report about 82–95% accuracy on small test sets. The 95% figure was achieved by matching against a known route list. Hard distance and latency figures were rarely visible in abstracts, and no field-deployed app was found.

### Cited Findings
- A pipeline of detection, image enhancement and OCR with route-list post-matching reported 95% accuracy on 120 images with poor lighting, reflections and blur (MDPI *Algorithms* 2025, DOI 10.3390/a18100616) — [Crossref record](https://api.crossref.org/works/10.3390%2FA18100616). The detail comes from a search snippet and could not be fetched.
- Mamba-based PP-OCR with super-resolution for bus route numbers (2026) reported 85.1% accuracy on the low-quality BusLED-700 dataset — [Univ. of Nottingham Ningbo](https://research.nottingham.edu.cn/en/publications/mamba-based-pp-ocr-enhanced-with-super-resolution-for-bus-route-n/)
- An approach using SIFT features and OCR reported 82.68–87.63% accuracy across five classifiers — [Springer 2023](https://link.springer.com/chapter/10.1007/978-981-99-3656-4_8)
- *Sensors* 2024 (DOI 10.3390/s24196411): a YOLO-series bus detector followed by a separate text-detection and recognition model — [Crossref](https://api.crossref.org/works/10.3390%2FS24196411)
- Improved YOLOv10 for visually impaired users does bus detection and point-of-view classification, with better accuracy and lower compute than YOLOv10 — [KAB library/DOAJ](https://library.kab.ac.ug/Record/doaj-art-2b4a4cf969ef493fb9d05cd086a17eb9/Description)
- An embedded on-phone system (ICT Innovations 2022) works offline and uses three models: line-number localization, recognition and TTS — [paper PDF](https://proceedings.ictinnovations.org/attachment/paper/559/embedded-deep-learning-to-aid-the-mobility-of-individuals-with-disabilities-a-solution-for-in-house-bus-line-recognition.pdf)
- A SPIE paper (Shufei Lin) reports the number *and distance* of inbound buses to visually impaired users — [SPIE profile](https://journals.spiedigitallibrary.org/profile/Shufei.Lin-4123783)
- A 2011 smartphone system ran in real time, with no percentage given — [UniFI FLORE](https://flore.unifi.it/handle/2158/792329)

### Inferences
- Matching OCR output against the list of routes that serve that stop (from GTFS) is the most effective accuracy lever. This is a strong design hint for fusing camera recognition with real-time feed data.
- Chișinău trolleybuses and buses mostly use LED route displays. The BusLED-700 work is directly relevant.

### Gaps
- Distance in metres and on-device latency were not extracted, because full texts could not be fetched.
- No field trials with blind users were found in these results.

## 5. Existing projects in Moldova or Romania for blind transit users

### Takeaway
No Moldovan project specifically for blind bus riders was found. Moldova has open real-time infrastructure (roataway: open-source, MQTT/GTFS; Tranzy open data; the city platform) but no accessibility layer on top of it. Romania has live precedents in Bucharest STEP HEAR (2025) and Onyx Beacon/RATB (2015).

### Cited Findings
- roataway is an open-source Chișinău transport tracker with a GTFS static repo, infrastructure data and an API exposing real-time trolleybus positions via MQTT. Not all vehicles have trackers, so `tracker_id` is empty for some — [GitHub infrastructure-data](https://github.com/roataway/infrastructure-data); [GitHub gtfs-data](https://github.com/roataway/gtfs-data); [API docs](https://uithub.com/roataway/api-documentation); [IPN on launch](https://ipn.md/en/platform-enabling-to-monitor-each-trolleybus-in-chisinau-in-real-time-to-be-laun-7967_1089295.html)
- A UNDP-linked pilot (2021) estimated real-time arrivals using local data, with Regia Transport Electric Chișinău as partner — [UNDP](https://undp.cz/?p=9655)
- The EU-funded "MOVE IT Like Lublin" initiative aims at a real-time GPS dashboard for Chișinău buses and trolleybuses — [EU project page](https://eu4georgia.eu/projects/eu-project-page/?id=1482)
- Asociația Nevăzătorilor din Moldova took part in designing a tactile-path park in Telecentru. No transit app by the association was found — [Unghiul](https://unghiul.com/in-chisinau-a-aparut-un-parc-accesibil-si-pentru-persoanele-cu-deficiente-de-vedere)
- Romanian SeeYou app (volunteer guides for visually impaired users) expanded from Bucharest to Cluj-Napoca in May 2025 — [Romania Insider](https://www.romania-insider.com/see-you-visually-impaired-guides-romania-2025)
- A young developer with a disability in Cluj built a public-transport app (local press) — [Actual de Cluj](https://actualdecluj.ro/video-un-tanar-cu-dizabilitate-din-cluj-se-pricepe-la-programare-mai-bine-decat-tine-si-face-in-joaca-aplicatie-de-transport-in-comun/)

### Inferences
- A Chișinău solution can integrate existing real-time sources (city platform/Tranzy, roataway) instead of building tracking from scratch. What is missing is the blind-specific last 50 m, meaning which vehicle is arriving now, plus Romanian/Russian voice output and possibly a driver alert.
- STEP HEAR (Bucharest) is the closest regional analogue and a possible partner, vendor or benchmark.

### Gaps
- No sources were found on Orange Moldova, Moldcell or Tekwill hackathon projects for blind transit users. Searches did not surface any.
- No usage data or current maintenance status was found for roataway. Its newest artifacts appear to be from around 2025.

## 6. Pricing, platform, languages

### Takeaway
User apps are almost all free: Moovit, NaviLens GO, OKO, Be My AI, Seeing AI, Lookout. The cost falls on the agency (NaviLens codes, beacons). Romanian and Russian voice support is undocumented for every blind-specific product found. NaviLens claims 42 languages, but the list was not verified.

### Cited Findings
- The NaviLens GO app is free, and the vendor claims 42 languages — [NaviLens EMT Madrid](https://www.navilens.com/en/case-studies/emt-madrid)
- OKO is free and iPhone-first, with Android in development as of the 2023 sources — [Innovation Origins](https://innovationorigins.com/en/free-ticker-app-oko-from-ayes-allows-the-blind-and-visually-impaired-to-cross-roads-safely-again); [AppleVis](https://applevis.com/comment/130853)
- Moovit gave WeWALK its transit data at no cost (reported) — [Moovit blog](https://moovit.com/blog/moovit-wewalk-partnership)
- Lookout launched on Pixel phones in the US only. Current availability needs checking — [PhoneArena](https://www.phonearena.com/news/google-lookout-app-pixel-devices-blind-visually-impaired-users_id114492)

### Inferences
- Romanian and Russian TTS would probably come from the OS voices (iOS and Android both ship Romanian and Russian voices). This is not verified for any specific app.

### Gaps
- No agency pricing for NaviLens, STEP HEAR or Onyx Beacon was found.
- No confirmed Romanian or Russian UI or voice support was found for NaviLens, Moovit's blind features, Lazarillo or BlindSquare.
