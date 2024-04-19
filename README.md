# IEA Wind Task 49 Floating Array Ontology

This repository will hold the definition and tools for the floating array 
ontology--a way of recording information that describes a floating wind 
farm project, including both site condition information and design information. 
This ontology is in a draft form and will continue to be revised based
on feedback from prospective users and collaborating projects.

The goal of the ontology is to provide a standardized format for recording
and exchanging a description of a floating wind farm design. This capability
is aligned with the work of IEA Wind Task 49, which focuses on integrated 
design of floating wind arrays. The ontology proposed here draws on elements
from two established ontologies developed under a previous IEA Wind Task.
Task 37 developed [plant-level and turbine-level ontologies](https://windio.readthedocs.io).
The current floating array ontology has a number of additions and differences that
better suit the scope and emphasis of floating wind arrays. 

This readme provides an overview of the ontology to facilitate feedback
and iteration. It is not yet finalized. Comments are welcome in the Issues 
page. The current ontology sections are as follows:

* [Site](#site)
  * [General                         ](#general)
  * [Boundaries                      ](#boundaries)
  * [Exclusions                      ](#exclusions)
  * [Bathymetry                      ](#bathymetry)
  * [Seabed                          ](#seabed)
  * [Metocean                        ](#metocean)
  * [Resource                        ](#resource)
  * [RAFT Cases                      ](#raft-cases)
  * [RAFT Settings                   ](#raft-settings)
* [Array                             ](#array)
  * [Array Layout                    ](#array-layout)
  * [Array Mooring                   ](#array-mooring)
  * [Array Cables                    ](#array-cables)
* [Turbine(s)                        ](#turbines)
* [Platform(s)                       ](#platforms)
* [Mooring                           ](#mooring)
  * [Mooring Systems                 ](#mooring-systems)
  * [Mooring line configurations     ](#mooring-line-configurations)
  * [Mooring line section properties ](#mooring-line-section-properties)
  * [Mooring Connectors              ](#mooring-connectors)
  * [Anchor types                    ](#anchor-types)
* [Cables                            ](#cables)
  * [Cables with Routing             ](#cables-with-routing)
  * [Dynamic Cable Configurations    ](#dynamic-cable-configurations)
  * [Cable Cross Sectional Properties](#cable-cross-sectional-properties)
  * [Cable Appendages                ](#cable-appendages)

The following sections give an overview of the array ontology makeup with examples. 


## Site
The site section contains information on the site conditions, including the boundaries of the array and exclusion zones. It also contains seabed conditions,
metocean data, and wind resource information for the selected site. 

### General
The general section includes water depth, density of water, density of air, and viscosity of air.

```yaml
    general:
        water_depth : 200        # [m]      uniform water depth
        rho_water   : 1025.0     # [kg/m^3] water density
        rho_air     : 1.225      # [kg/m^3] air density
        mu_air      : 1.81e-05   #          air dynamic 
```

### Boundaries
The boundaries section contains the boundaries of the array. This information is provided with a list of polygon vertices in order, which are then connected linearly 
to define the boundaries of the array. This information can be used to check that all the floating wind turbines are fully contained within the array boundaries.

```yaml
    boundaries:  # project or lease area boundary, via file or vertex list
        file:   # filename of x-y vertex coordinates [m]
        x_y:    # list of polygon vertices in order [m]
           -[x1, y1]
           -[x2, y2]
           -[x3, y3]
           -[x4, y4]
```

### Exclusions
The exclusions section contains any information on exclusion zones, which can be used to check that the power cables and anchors are avoiding
those areas. For simplicity, the exclusion zones allow for two types: circle and polygon. The circle option allows the user to input the x,y coordinates
of the center of the circle and the radius of the circle. The polygon option allows the user to input a list of x,y coordinates which are then 
connected linearly to define the exclusion zone. The user can define as many exclusion zones as needed with a "name" to distinguish them. 

```yaml
    exclusions:
      - name:
        type: circle
        x_y_r:
          - [x1, y1, r1] 
      
      - name:
        type: polygon
        x_y_r:
            -[x1, y1]
            -[x2, y2]
```

### Bathymetry
The bathymetry section currently just provides a link to a MoorDyn-style
bathymetry grid file. There is potential redundancy with the seabed section
and we likely want to support other file formats as well. To be improved.
	
```yaml
    bathymetry:
        file:   # MoorDyn-style bathymetry file
```


### Seabed
The seabed section contains information on the depth and soil type throughout the array. 
The user provides a list of x, y, z and "soil type" points to define the depth
and soil type classification. Alternatively, a file can be specified containing fully
gridded data about depth and soil properties on a rectangular grid, with the option
for quantitative soil index properties.
In between provided points, the depth is linearly interpolated. 
For now, the soil type will be assumed to match that of the nearest point. 
	
```yaml
    seabed:
        keys: [x,  y, depth,  soil_type]
        data:
          - [ x1, y1, z1, "soft clay"]
          - [ x2, y2, z1, "medium clay"]    
          
        filename: 'seabed_grid.csv'  # gridded data of seabed depth and soil classification   
```

### Metocean
The metocean section contains wind, wave, and current parameters that inform the design load cases. This section is further subdivided into extremes, probabilistics bins, and time series.
The extreme subsection contains metocean parameters for specified return periods which are used to check the strength of the design in extreme conditions.
The probabilistic bins section contains a list of metocean parameters, including directionality and probability. This section is used to inform a fatigue analysis,
so the probability of the bins should total to 1. Finally, the timeseries section contains the metocean parameters in time series form, which 
is needed for logistics analysis to inform vessel availability. To reduce the number of lines in the file, the timeseries section inputs a 
csv filename. 

```yaml
    metocean:
        extremes:  # extreme values for specified return periods (in years)
            keys :   [ Hs  , Tp  , WindSpeed, TI, Shear, Gamma, CurrentSpeed ]
            data :
                1:   [     ,     ,     ]
                10:  [     ,     ,     ]
                50:  [     ,     ,     ]
                500: [     ,     ,     ]
                
        probabalistic_bins:
            keys : [ prob , Hs  , Tp, WindSpeed, TI, Shear, Gamma, CurrentSpeed, WindDir, WaveDir, CurrentDir  ]
            data :
                -  [ 0.010  ,   ,   ]
                -  [ 0.006  ,   ,   ]
                -  [ 0.005  ,   ,   ]
                
        time_series :
            filename: 'metocean_timeseries.csv'
```

### Resource
The resource section contains information on the wind resource for the site, 
which can be used to calculate AEP. To again reduce the number of lines
in the file, the resource section inputs a filename which contains the resource data.
This resource data will follow the [WindIO plant ontology](https://windio.readthedocs.io/en/latest/source/plant.html).

```yaml
    resource :
        filename: 'windresource'
```

### RAFT Cases
The RAFT cases section contains the parameters for any load cases that are intended to be run in [RAFT](https://openraft.readthedocs.io), and as such follows the format specified by RAFT. 
The section inputs a list where each entry corresponds to a load case. Note that turbulence can be input 
as a percent or as a string representing a turbulence model such as IIB_NTM.
```yaml
RAFT_cases:
        keys : [wind_speed, wind_heading, turbulence, turbine_status, yaw_misalign, wave_spectrum, wave_period, wave_height, wave_heading  ]
        data :  #   m/s        deg    % or e.g. IIB_NTM    string            deg         string          (s)         (m)         (deg)
            -  [    10.5,         0,            0.01,    operating,          0,        JONSWAP,         12,         6,         0       ]
```

### RAFT Settings
The RAFT settings section contains the general parameters used for RAFT simulations, such as cutoff frequencies, 
Initial amplitudes for each degree of freedom at all frequencies, and the number of iterations to solve the model dynamics. 
As with the previous section, the format follows that specified by [RAFT](https://openraft.readthedocs.io).
```yaml
RAFT_settings:   
        min_freq     :  0.001    #  [Hz]       lowest frequency to consider, also the frequency bin width     
        max_freq     :  0.10    #  [Hz]       highest frequency to consider
        XiStart      :   0      # sets initial amplitude of each DOF for all frequencies
        nIter        :   4      # sets how many iterations to perform in Model.solveDynamics()
``` 

## Array

This part of the ontology includes a section for the tubine layout, as
well as optional array-level descriptions of the mooring system and 
array cabling.

### Array Layout
The array section summarizes the floating wind turbines in the array. The 
section inputs a list where each entry corresponds to a wind turbine. The ID serves as a method to identify the specific turbine system. 
As such, each list entry should have a unique ID number. The turbineID and platformID are specified for each list entry,
connecting to details in the [Turbine](#turbines) and [Platform](#platforms) sections. This allows the user to easily 
specify different turbine or platform types throughout the array. 
Similarly, the mooringID is included and refers to the [mooring_systems](#mooring-systems) section.
This allows the user to set up general mooring systems to be used throughout the array. Additionally, the x and y locations are input and the heading adjustment.
The heading adjustment refers to a rotation of the mooring system, relative to how it is defined in the mooring_systems section. This allows the user to 
easily define a single mooring system for various rotations throughout the array.

Alternatively, the mooringID can be set to zero and the mooring system can be 
input in the [array_mooring](#array-mooring) section.

```yaml
array:
    keys : [ID, turbineID, platformID, mooringID,   x_location,     y_location,   heading_adjust]
    data : # ID#   ID#        ID#        ID#           [m]             [m]           [deg]
        -  [1,     1,         1,         ms1,         0,             0,           180  ]    
        -  [2,     1,         2,         ms2,         1600,          0,            0   ]  
```


### Array Mooring
The array mooring section allows the user to input array-level mooring system details, instead of the more generalized mooring systems in mooring_systems.
This section inputs a list of x,y anchor positions, anchor type, and embedment depth. The anchor type links to the list in the anchor_types section.
Additionally, a list of mooring lines can be input with specified attachments at FOWTs and anchors. To specify an anchor, use 'ANCH #' where # refers to the anchor ID in the anchor_data table.
If there is an anchor connected to this line, it must be listed in end A, not end B. To specify a FOWT, use 'FOWT #' where # refers to the system ID in the array table. 
The mooring lines each have a mooring configuration ID which links to the mooring_line_configs section. 
There is also an option to adjust the length of the line, depending on the spacing. 

```yaml
array_mooring:
    anchor_keys : 
          [ID, type,  x,  y,  embedment ]
    anchor_data :
        - [  1,  suction1,   ,   ,     ]
        - [  2,  suction1,   ,   ,     ]
    
    line_keys : 
          [MooringConfigID  ,  end A,   end B,  lengthAdjust]
    line_data :
        - [ Taut-Poly_1      ,  ANCH 1,  FOWT 1,   0]
        - [ Taut-Poly_1      ,  ANCH 2,  FOWT 1,   0]
        - [ Taut-Poly_2      ,  FOWT 1,  FOWT 2,   0]

```

### Array Cables

This section provides a straightforward and compact way to define the power
cables in the array. For each end (A and B) of the cable, it specifies the
turbine attached to, the 
[dynamic cable configuration](#dynamic-cable-configurations) used, and the heading
of the dynamic cable. The type of the static cable is also specified.
This method does not consider routing, and would assume the static cable takes
a straight path from the ends of the dynamic cables. 
For additional detail related to cable routing, the alternative [cable](#cables-with-routing)
section should be used.

```yaml
array_cables:
    keys:  [ AttachA,  AttachB,  DynCableA,  DynCableB, headingA, headingB, cableType]
    data:
        - [ turbine1, turbine2, lazy_wave1, lazy_wave1,      180,       30, static_36] 
        - [ turbine2, turbine3, lazy_wave1, lazy_wave1,      150,       30, static_36] 
```

## Turbine(s)

The turbine section can contain either a single turbine or a list of turbines, 
depending on the scenario. Note that if multiple turbines are listed, the section title must be 'turbines' instead of 'turbine'.
By default, the format follows that of [RAFT](https://openraft.readthedocs.io)
However, support will be added for linking to turbine design descriptions that follow
the [WindIO](https://windio.readthedocs.io) ontology format, which is also used 
by [WEIS](https://weis.readthedocs.io).

## Platform(s)

This section defines the floating support structures used in the design. As in
the previous section, it can contain a single platform or a list of platforms. 
By default, the format here follows that used by 
[RAFT](https://openraft.readthedocs.io) input files.
However, support will be added for also linking to platform descriptions that follow
the [WindIO](https://windio.readthedocs.io) ontology format, which is also used 
by [WEIS](https://weis.readthedocs.io).

```yaml 
platform:

    potModMaster :   1      # [int] master switch for potMod variables; 0=keeps all member potMod vars the same, 1=turns all potMod vars to False (no HAMS), 2=turns all potMod vars to True (no strip)
    dlsMax       :  5.0     # maximum node splitting section amount for platform members; can't be 0
    
    members:   # list all members here
        
      - name      :  center_column             # [-]    an identifier (no longer has to be number)       
        type      :  2                         # [-]    
        rA        :  [ 0, 0, -20]              # [m]    end A coordinates
        rB        :  [ 0, 0,  15]              # [m]    and B coordinates
        shape     :  circ                      # [-]    circular or rectangular
        gamma     :  0.0                       # [deg]  twist angle about the member's z-axis
        potMod    :  True                      # [bool] Whether to model the member with potential flow (BEM model) plus viscous drag or purely strip theory
        # --- outer shell including hydro---
        stations  :  [0, 1]                    # [-]    location of stations along axis. Will be normalized such that start value maps to rA and end value to rB
        d         :  10.0                      # [m]    diameters if circular or side lengths if rectangular (can be pairs)
        t         :  0.05                      # [m]    wall thicknesses (scalar or list of same length as stations)
        Cd        :  0.6                       # [-]    transverse drag coefficient       (optional, scalar or list of same length as stations)
        Ca        :  0.93                      # [-]    transverse added mass coefficient (optional, scalar or list of same length as stations)
        CdEnd     :  0.6                       # [-]    end axial drag coefficient        (optional, scalar or list of same length as stations)
        CaEnd     :  1.0                       # [-]    end axial added mass coefficient  (optional, scalar or list of same length as stations)
        rho_shell :  7850                      # [kg/m3] 
        # --- handling of end caps or any internal structures if we need them ---
        cap_stations :  [ 0    ]               # [m]  location along member of any inner structures (in same scaling as set by 'stations')
        cap_t        :  [ 0.001  ]             # [m]  thickness of any internal structures
        cap_d_in     :  [ 0    ]               # [m]  inner diameter of internal structures (0 for full cap/bulkhead, >0 for a ring shape)

```


## Mooring

This part of the ontology contains a set of sections that define parts of the 
mooring system, down to the definition of line cross sectional properties and 
anchor characteristics.

### Mooring Systems

This section describes the mooring systems that could be used for individual turbines and repeated throughout the array. Each mooring system contains a 
list of mooring lines, which contains the mooring configuration ID, the heading, the anchor type, and a possible length adjustment. The 
mooring configuration ID links to the details about the segments lengths and types in the mooring line configurations section. The heading refers to the angle of the mooring line and it rotates 
counterclockwise from +X. The anchor type links to details about the anchor 
size and dimensions in the [anchor types section](#anchor-types). The length adjustment
is an optional parameter that can adjust the mooring line length for a shallower or deeper depth, for example. 

```yaml
mooring_systems:
    ms1:
        name: 3-line taut polyester mooring system
        
        keys: [MooringConfigID,  heading, anchorType, lengthAdjust] 
        data:
          - [  taut-poly_1,   60 ,    suction 1,   0 ]
          - [  taut-poly_1,  180 ,    suction 1,   0 ]
          - [  taut-poly_1,  300 ,    suction 1,   0 ]
```

### Mooring Line Configurations

The mooring line configurations lists the segment lengths and line types that make up each mooring line. Each line has a name that can then be specified 
as the MooringConfigID in the mooring systems section. The anchoring radius (also known as the span), fairlead radius, and fairlead depth are also specified for each line configuration.
 Each line contains a list of sections that details the line section type and length. The line type name
connects to information in the mooring [line section properties](#mooring-line-section-properties). 
Additionally, before and after each line section has an optional input which can list the 
ID of a [connector type](#mooring-connectors), such as an H-link or buoy. A connector is specified by using the key connectorType, while a line section is specified using the key type.
This information allows the weight and buoyancy of the connections to be included 
in the model, and provides clarity on the location of the connector relative to different line sections. 
Shared lines may have a 'symmetric' key used to describe whether the provided line configuration is half of a symmetric line 
(symmetric: True) or a full line configuration (symmetric: False).If a connector is given 
as the last item in the section list for a shared symmetric line, it is assumed that the provided connector is located in the center of the line.
There is also a True/False options for whether the section length is adjustable. Note that line sections and connectors should be added to the sections list in order from end A to end B.



```yaml
  mooring_line_configs:
    
    taut-poly_1:  # mooring line configuration identifier
    
        name: Taut polyester configuration 1  # descriptive name		
		
		anchoring_radius: 1131.37
        fairlead_radius: 40.5
        fairlead_depth: -20
        
        sections:
		  - connectorType: shackke # ID of a connector type (optional)
          - type: chain_160       # ID of a mooring line section type
            length: 80            # [m] usntretched length of line section
            adjustable: True      # flags that this section could be adjusted to accommodate different spacings...			
		  - connectorType: h_link    # ID of a connector type (optional)       
          - type: poly_180        # ID of a mooring line section type
            length: 762           # [m] length (unstretched)			
          - connectorType: shackle    # ID of a connector type (optional)


    shared-2-clump:
        name: Shared line with two clump weights
        symmetric: True		
		
		anchoring_radius: 1142
        fairlead_radius: 58
        fairlead_depth: -14
        
        sections:
          - type: poly_180   
            length: 80   			
          - connectorType: clump_weight_20            
          - type: poly_180
            length: 762   

```    
    
### Mooring line section properties

The mooring line section properties contains the properties needed to accurately model the mooring lines. Each mooring line type is listed with 
a name that can be referenced in the [mooring line configurations section](#mooring-line-configurations). 
For each line type, the nominal and volume equivalent diameter are listed, 
as well as the mass density, stiffness, cost, MBL, and material name. The ontology supports either a single stiffness value (like for chain)
or the static-dynamic stiffness of fiber lines. An example of this is shown below. 

Alternatively, the mooring line parameters can be provided in a table-based format to reduce the number of lines.

```yaml
mooring_line_types:

    polyester_226mm:
        d_nom:    0.262      # [m] nominal diameter
        d_vol:    0.2258     # [m] volume-equivalent diameter
        m:        55.0       # [kg/m] mass per unit length (linear density)
        EA:       164e6      # [N] quasi-static stiffness
        MBL:    11.75e6      # [N] minimum breaking load
        EAd:    164.6e6      # [N] dynamic stiffness
        EAd_Lm:    0.34      # [-] dynamic stiffness mean-load multiplier
        cost:      194       # [$/m] cost per unit length
        material: polyester  # [-] material composition descriptor
        
    chain_170mm::
        d_nom:    0.170      # [m] nominal diameter
        d_vol:    0.306      # [m] volume-equivalent diameter
        m:        575.0      # [kg/m] mass per unit length (linear density)
        EA:      2468e6      # [N] quasi-static stiffness
        MBL:     25.2e6      # [N] minimum breaking load
        cost:      1486      # [$/m] cost per unit length
        material:  R3 studless chain  # [-] material composition descriptor

    # alternative table-based format
    keys :  [ name,   EA ,  MBL,  ...]
    data :
        -   [ poly1  , 3e7, 10e6, ... ]
        -   [ chain27, 3e9, 10e6, ... ]
```

### Mooring Connectors

This section lists properties of the mooring connections that are referenced in the mooring line configurations section. 
Each connector has a name that is used to identify it, as well as a mass and volume. Optionally, the CdA of the connector 
can be specified to model the drag on the component. 
```yaml
 mooring_connector_types:
    
    h_link:
        mass   : 140    # [kg]  component mass
        volume : 0.13   # [m^3] component volumetric displacement
        
    clump_weight_20:
        mass   : 20000  # [kg]
        volume :  0.8   # [m^3]
        
    buoy_10:
        mass   :  560   # [kg]  component mass
        volume : 10.2   # [m^3] component volumetric displacement
        CdA    :  3.5   # [m^2] product of cross sectional area and drag coefficient
```

## Anchor types

The anchor types section lists dimensions and embedment depth for each anchor type. The anchor types section
allows the user to input the diameter, length, area, thickness, and embedment depth. All parameters are optional,
because the applicable information depends on the anchor type. 
The parameters align with the FAModel 
[intermediate anchor model](https://github.com/FloatingArrayDesign/FAModel/tree/main/famodel/anchors#parameters-needed-for-level-2-anchor-capacity-models). 

```yaml        
anchor_types:
    suction1:
        name   : standard suction pile
        d      :    # [m] Diameter
        L      :    # [m] Length
        t      :    # [mm] Thickness
        h      :    # [m] Embedment depth
```


## Cables

This section describes the cables through the array including both static 
and dynamic portions of cables. At the top level, each array cable going
between a pair of turbines (or a turbine and a substation) is defined. 
This definition can either occur in the [array_cables](#array-cables) 
section or the [cables](#cables-with-routing) section. 
The latter provides additional options for defining
the cable routing and burial depth.

### Cables with Routing

The cables section contains a list of every cable in the array. Here, a cable
is defined as the full assembly of electrical connection equipment between
two turbines or a turbine and a substation. Similar to the [array_cables](#array-cables) 
section, 'type' links to the cross-section property description of the static
portion of the cable. endA and endB sections define what each end of the cable
is attached to, at what heading it comes off at, and what dynamic cable
profile it uses. Additional fields specify the routing of the static portion
of the cable and the burial depth as as function of cable length.

```yaml
 cables:

  - name : array_cable1      # descriptive cable name
    type : static_cable_80   # cable section type ID
    
    endA: 
        attachID: turbine_1            # FOWT/substation/junction ID
        heading:  180                  # [deg] heading of attachment at end A
        dynamicID: dynamic_lazy_wave1  # ID of dynamic cable configuration at this end
    
    endB:
        attachID: turbine_2            # FOWT/substation/junction ID
        heading:  30                   # [deg] heading of attachment at end B
        dynamicID: dynamic_lazy_wave1  # ID of dynamic cable configuration at this end
    
    routing_x_y_r:  # optional vertex points along the cable route. Nonzero radius wraps around a point at that radius.
      - [1000, 1200, 20] 
      - [2000, 1500, 20] 
    
    burial:  # optional definition of cable burial depth over its length
        station: [0, 1]                # length along cable, normalized by first and last value
        depth  : [0.1, 0.2]            # [m] burial depth

  - name : array_cable_2     # descriptive cable name
    type : static_cable_80   # cable section type ID
    ...
```

## Dynamic Cable Configurations

This section lists the dynamic cable configurations used in the array design.
Similar to the mooring_line_configs section, it details the assembly of 
cable section that make up a dynamic cable profile, with links to the cross
sectional cable properties. Dynamic cable configurations have some special
properties including specification of the voltage, and the option of 
specifying 'appendages' along the cable length, which can represent [discrete
objects](#cable-appendages) like buoyancy modules.

```yaml
 dynamic_cable_configs:

    lazy_wave1
        name: Lazy wave configuration 1 (simpler approach)
        voltage: 66 # [kV]
        span :     # [m] horizontal distance to end of dynamic cable
        
        sections:
          - type: dynamic_cable_27        # ID of a cable section type1
            length: 200                   # [m] length (unstretched)
            
          - type: dynamic_cable_27_w_buoy # (section properties including averaged effect of buoyancy modules)
            length: 300                  
            
          - type: dynamic_cable_27 
            length: 200            
            
        attachment:
            type: j-tube
            coordinate:   # relative location
    

    lazy_wave2
        name: Lazy wave configuration 1 (more detailed approach)
        voltage: # [kV]
        span :     # [m] horizontal distance to end of dynamic cable
        
        sections:
          - type: dynamic_cable_27        # ID of a cable section type1
            length: 200                   # [m] length (unstretched)
            appendages:
                type: buoyancy_module_1
                locations: [10,12,13.5,15,18]
                
        attachment:
            type: j-tube
            coordinate:   # relative location
```       
    
### Cable Cross Sectional Properties

This section details the cross-sectional properties of each cable type.

```yaml
cable_types:

    dynamic_cable_66 :     # cable type identifier        
        dynamic :   True   # Flag for dynamic cable (default static)
        DC   :     False   # Flag for DC (default AC)
        kV   :        66   # [kV] voltage rating
        A    :       300   # [mm^2] cross-sectional area of each conductor (3 conductors)
        D    :      0.20   # [m] outer diameter
        m    :     30.59   # [kg/m] mass per unit length
        EA   :    700e+3   # [kN] axial stiffness 
        EI   :      10.0   # [kN.m^2] bending stiffness
        MBL  :       100   # [kN] minimum breaking load
        MBR  :       2.0   # [m] minimum bending radius

    static_cable_36:  
        dynamic :  False
        DC   :     False	
        kV   :        36		
        A    :       300		
        D    :    0.1248		
        m    :     12.90		
        EA   :    245e+3		
        EI   :      5.10		
        MBL  :      54.0		
        MBR  :     1.875		
```

### Cable Appendages

This section lists any cable appendages that might be added to the cables,
such as buoyancy modules or cable protection system components. Each entry
is given an identifier and can have a variety of parameters that describe
its lumped properties, such as mass, volume, and drag coefficient-area
product. These appendages are used in the 
[dynamic_cable_configs](#dynamic-cable-configurations) section.

```yaml
  cable_appendages:

    buoyancy_module_1:
        mass:    2700   # [kg]  mass
        volume: 8.615   # [m^3] volumetric displacement 
        CdA:     3.8    # [m^2] product of cross-sectional area and drag coefficient
        length:  2.2    # [m]   length taked up along cable
```
