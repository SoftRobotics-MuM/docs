# CAD Files
All CAD files are stored in the [cloud](https://cloud.tuhh.de/index.php).

CAD models are stored in the native Autodesk Inventor formats (`.ipt` for parts and `.iam` for assemblies).

To avoid compatibility issues between different Inventor versions, all CAD files uploaded to the cloud must be created and edited using **Autodesk Inventor 2024**.

## File Structure
To ensure a clean and organized workflow, it is important to maintain the file structure.

The `CAD_Source` directory is divided into two main sections: `10_Common_Candidates` and `20_Robots`.

### Common Candidates

`10_Common_Candidates` contains components that are used across different robot designs. These are mainly components of the robot base.

### Robots

`20_Robots` contains one directory for each robot design.

Each robot is identified by a short identifier at the beginning of its directory name.

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
└── 20_Molding

```

- **`00_Documentation`** – Contains documentation related to the robot, such as manufacturing instructions and bills of materials.
- **`10_Product`** – Contains the CAD models defining the robot.
  - **`Assemblies`** – Contains Inventor assembly files (`.iam`).
  - **`Parts`** – Contains parts that are 3D printed (`.ipt`).
  - **`Reference_Models`** – Contains reference geometry such as silicone bodies and purchased parts.
- **`20_Molding`** – Contains CAD files related to the manufacturing of molds for the robot.

## Part and File Naming

**Part ID** follow the structure:

`<scope>-<type>-<number>`

The first identifier specifies the scope of the part:

- `CC` – Part belongs to `10_Common_Candidates`
- For robot-specific parts, the scope identifier corresponds to the identifier at the beginning of the respective robot directory name.

For example, parts in `RL_Robot_introductions-to-robotics` use the scope identifier `RL`.

The type identifier indicates the corresponding directory:

- `P` – `Parts`
- `REF` – `Reference_Models`
- `M` – `Molding`
- `IMP` – `CAD_imports`


The corresponding CAD **file name** follows the structure:

`<part-ID>_<description>.<file-extension>`

For example:

`RR-P-001_tendon_holder_top.ipt`

- `RR` – Scope identifier for `RR_Robot_retractable`
- `P` – Part stored in `Parts`
- `001` – Sequential part number
- `tendon_holder_top` – Descriptive file name
- `.ipt` – Autodesk Inventor part file
