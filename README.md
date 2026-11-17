# IEA Wind Task 49 Offshore Array Ontology

<div align="justify">
The IEA Wind Task 49 is actively developing an offshore array ontology—a standard, tool-agnostic 
data format for describing the design of an offshore wind farm including the 
layout, the offshore structures and systems, and the site characteristics. 
The aim is to facilitate clear communication of pre-FEED-level information, 
making it easier to share data and collaborate on offshore wind projects and 
studies.
</div>

<br>

<div align="justify">
Development of the ontology is currently in a feedback stage, where we seek
input from industry, researchers, and other potential users. To review the 
current plan for the ontology's requirements and scope, see [.docx file to
be added].
</div>

<br>

Feedback is welcome, either through emailed comments to [email TBD] or through issue postings in the Issues tab.

<br>

> [!NOTE]
> For those looking for the ontology description from Phase 1, see the
> [Phase 1 subfolder](Phase1/).

<br>

## Ontology Use Cases

| | |
| --- | --- |
| Exchange design information between groups or tools | Compare performance of different methods |
| Conduct optimization studies | Analyze annual energy production (AEP) |
| Perform component-level analysis and design | Couple and integrate design across subsystems, extending to array-level design |
| Logistics modeling of marine operations such as installation, operations and maintenance (O&M), and decommissioning | Research monitoring and digital twin applications |
| Study farm-aware and farm-level controls | Analyze marine spatial planning scenarios |
| Conduct techno-economic modeling | Assess fabrication and manufacturability |
| Assess socio-economic impacts | Assess environmental impacts through life-cycle assessment and analysis of wildlife, bird-collision, seabed, and noise interactions |
| Study Power-to-X applications |  |
| | |

<br>

## Supported software
| Software | Utility |
| --- | --- |
| [PyWake](https://topfarm.pages.windenergy.dtu.dk/PyWake/) | --- |
| will add more here ... | --- |
| ... | --- |
| ... | --- |

<br>

## Ontology structure

Here, we list the information to be included/supported by the ontology. The ontology is split into two top-level groups: **design information** and **site information**. Section names follow IEC 60050 ([Electropedia](https://www.electropedia.org/)) and ISO ([Online Browsing Platform](https://www.iso.org/obp)) terminology where a matching term exists; the IEV number or ISO standard is given in the last column.

<br>

> [!NOTE]
> The tables below give a high-level view only. Detailed parameter lists are
> defined in the ontology schema itself.

<br>

### Design information

| Section | Purpose | Content | Standard reference |
| --- | --- | --- | --- |
| **Wind power station** (array layout) | Describes the whole array and where each asset sits | Metadata (rated power, number of units); nominal positions and orientations of wind turbines, substations and standalone instruments, each with their station-keeping system; inter-array cable routing. | [IEV 415-01-03](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-01-03) wind power station / wind farm |
| **Wind turbine** (rotor-nacelle assembly and tower) | Turbine data needed for various fidelity studies | Rotor diameter, hub height, mass and centre of gravity; power and thrust coefficient curves; pitch and yaw control systems; cost estimates. | [IEV 415-01-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-01-01) wind turbine; [415-05-06](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-05-06) hub height; [415-05-03](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-05-03) power coefficient; [415-05-18](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-05-18) pitch angle |
| **Support structure** (substructure, platform and foundation) | Floating or fixed structure carrying a topside (turbine, substation, etc.) | List of structural elements described either as lumped bodies (mass, mesh, hydrodynamic data) or as members (cross-sections, wall thickness); interface points (fairleads, cable hang-offs, transition pieces); materials information; manufacturability identifiers (design and shared-part IDs, block masses) | [IEV 415-01-08](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-01-08) support structure; general support structure: [ISO 19900](https://www.iso.org/obp/ui/en/#iso:std:iso:19900:ed-3:v1:en), floating platform structure: [ISO 19904-1](https://www.iso.org/obp/ui/en/#iso:std:iso:19904:-1:ed-2:v1:en); offshore wind: [IEC 61400-3-1](https://webstore.iec.ch/en/publication/29360) (fixed), [IEC 61400-3-2](https://webstore.iec.ch/en/publication/67761) (floating) |
| **Foundation / anchor** | Seabed elements of the support or station-keeping system | Anchor type (pile, suction, plate, drag, screw, gravity, rock); geometry, embedment and tolerances; padeye/loading point; installation method; holding capacity or stiffness; embedded chain section | [ISO 19901-4](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-4:ed-2:v1:en), [ISO 19901-7](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-7:ed-2:v1:en)|
| **Station-keeping system** (mooring) | Keeps floating units on station | Line topology (headings, attachment to platforms and anchors); line configurations as assemblies of sections with clump weights or buoys; section properties (mass, diameter, stiffness, breaking load, fatigue curve, material and grade) | [ISO 19901-7](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-7:ed-2:v1:en) stationkeeping systems; offshore wind: [IEC 61400-3-2](https://webstore.iec.ch/en/publication/67761) (floating) |
| **Power collection system** (inter-array cables) | Connects wind turbines to the substation(s) | *Dynamic cables:* mechanical and electrical ratings, length, end attachments, buoyancy sections and accessories. *Static cables:* ratings, burial depth | [IEV 415-04-06](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-04-06) power collection system; [461-06-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=461-06-01) insulated cable |
| **Export cable** | Connects the offshore substation to the point of connection | Transmission technology (HVAC/HVDC), voltage and current rating, conductor resistance; mass, diameter, minimum bending radius, tensile strength | [IEV 466-01-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=466-01-01) electric line; [461-06-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=461-06-01) insulated cable |
| **Substation** (offshore, AC or converter) | Collects and transforms/converts power for export | Rated power, voltage levels and frequency, AC/DC, connected feeders and export cables; mass, centre of gravity, deck loads, interfaces with the substructure; access (helideck, cranes, persons on board); motion and airgap criteria; electrical architecture (busbar scheme, bays, converter type) and major components (transformers, switchgear) with reliability data | [IEV 605-01-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=605-01-01) substation; [605-01-07](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=605-01-07) converter substation; [421-01-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=421-01-01) power transformer; [441-11-02](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=441-11-02) switchgear |
| **Instrumentation** | Sensors on assets or standalone measurement buoys/masts | ID, mounting (asset ID + relative location, or absolute position), measured quantity, range, sampling rate, accuracy, purpose (structural health monitoring, environmental monitoring, control) | [IEV 311-05-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=311-05-01) sensor |
<!-- | **Shared libraries** | Definitions reused across the design to avoid duplication | Materials, mooring line and cable properties, anchor and component catalogues; reliability data (failure rates) | [IEV 192-05-06](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=192-05-06) failure rate | -->

### Site information

| Section | Purpose | Content | Standard reference |
| --- | --- | --- | --- |
| **Project area** | Hold area characteristics and boundaries | Site name and global coordinates; boundary vertices; exclusion/sensitivity zones (reason, boundary, depth band, excluded component types); neighbouring wind farms and other sea users (aquaculture, shipping routes, protected areas) | — |
| **Seabed** | Soil conditions for foundations, anchors and cables | Bathymetry grid; soil layers (homogeneous or layered) with properties for sand, clay/silt and rock | [ISO 19901-4](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-4:ed-2:v1:en) geotechnical and foundation design |
| **Metocean** | Environmental loading (for ultimate and fatigue limit states) and resource | Extreme conditions per return period; operational/fatigue bins; wind resource (wind rose, following the [windIO](https://ieawindsystems.github.io/windIO/main/index.html) plant schema); time series | [ISO 19901-1](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-1:ed-2:v1:en) metocean design; [IEV 415-02-03](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-02-03) external conditions; [415-03-16](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-03-16) wind shear|
| **Environmental degradation** | Deteriorating conditions of the assets that at site-specific rates over the assets' design life | Marine growth (thickness profile with depth, density); corrosion rates (may vary with depth); climate change effects | [ISO 19901-1](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-1:ed-2:v1:en) (marine growth); [ISO 8044](https://www.iso.org/obp/ui/en/#iso:std:iso:8044:ed-5:v1:en) corrosion |
| **Point of connection** (grid connection) | Boundary between the offshore array and the onshore grid | Location; thermal capacity and injection limit; voltage level and frequency; onshore substation/converter station | [IEV 617-04-01](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=617-04-01) point of connection; [415-04-05](https://www.electropedia.org/iev/iev.nsf/display?openform&ievref=415-04-05) network connection point |
| **Logistics** | Supports installation, O&M and decommissioning studies | Port locations and capabilities (berths, wet storage, quay load capacity, laydown area, water depth); manufacturing and supply-chain locations | [ISO 19901-6](https://www.iso.org/obp/ui/en/#iso:std:iso:19901:-6:ed-1:v1:en) marine operations |
