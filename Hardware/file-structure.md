# CAD Files
All CAD files are stored in the [cloud](https://cloud.tuhh.de/index.php).

CAD parts are stored in the native Autodesk Inventor formats (`.ipt` for parts and `.iam` for assemblies)
To avoid compatibility issues between different Inventor versions, all CAD files uploaded to the cloud must be created and edited using **Autodesk Inventor 2024**.

## File Structure
To ensure a clean and organized workflow, it is important to maintain the file structure.

The `CAD_Source` directory is divided into two main sections: `10_Common_Candidates` and `20_Robots`.

### Common Candidates

`10_Common_Candidates` contains components that are used across different robot designs. These are mainly components of the robot base.
Every part in this directory uses the prefix `CC` for the part ID and the corresponding CAD file name.

### Robots

`20_Robots` contains one directory for each robot design.

Each robot is identified by a two- or three-letter abbreviation at the beginning of its directory name. This identifier is also used as a prefix for the part ID and the corresponding CAD file name. For example, files belonging to the retractable robot use the prefix `RR`, such as:

`RR-P-001_tendon_holder_top.ipt`

```text
CAD_Source
├── 10_Common_Candidates
│   ├── CAD_imports
│   └── Parts
└── 20_Robots
    ├── RL_Robot_introductions-to-robotics
    ├── RR_Robot_retractable
    └── RT6_Robot_tapered-6strings
```

All robot directories follow the same internal structure. This structure is described below using one robot as an example.

```text
RR_Robot_retractable
├── 00_Documentation
├── 10_Product
│   ├── Assemblies
│   ├── Parts
│   └── Reference_Models
├── 20_Molding
└── 80_Experiments
```

- **`00_Documentation`** – Contains documentation related to the robot, such as manufacturing instructions and bills of materials
- **`10_Product`**
  - **`Assemblies`** – Contains Inventor assembly files (`.iam`).
  - **`Parts`** – Contains parts that are 3D printed (`.ipt`).
  - **`Reference_Models`** – Contains reference geometry such as silicone bodies and purchased parts.
- **`20_Molding`** – Contains CAD files related to the manufacturing of molds for the robot.

## Part and File Naming

Part IDs follow the general structure:

`<scope>-<type>-<number>`

The first identifier specifies the scope of the part:

- `CC` – Part belongs to `10_Common_Candidates`
- For robot-specific parts, the identifier corresponds to the abbreviation at the beginning of the respective robot directory name. (For example `RL` – `RL_Robot_introductions-to-robotics/`)


The type identifier indicates the corresponding folder:

- `P` – `Parts`
- `REF` – `Reference_Models`
- `M` – `Molding`
- `IMP` - `CAD_imports`
