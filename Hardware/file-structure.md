# CAD Files
All CAD files are stored in the [cloud](https://cloud.tuhh.de/index.php).

CAD parts are stored in the native Autodesk Inventor formats (`.ipt` for parts and `.iam` for assemblies)
To avoid compatibility issues between different Inventor versions, all CAD files uploaded to the cloud must be created and edited using **Autodesk Inventor 2024**.

## File Structure
To ensure a clean and organized workflow, it is important to maintain the file structure.

The `CAD_Source` directory is divided into two main sections: `10_Common_Candidates` and `20_Robots`.

### Common Candidates

`10_Common_Candidates` contains components that are used across different robot designs. These are mainly components of the robot base.
Every part in this directory uses the prefix CC_ for the part ID and the corresponding CAD file name.

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

The robot directories are organized as follows:

- **`00_Documentation`** – Contains documentation related to the robot, such as ...
- **`10_Product`** – Contains the CAD files that define the robot itself.
  - **`Assemblies`** – Contains Inventor assembly files (`.iam`).
  - **`Parts`** – Contains individual Inventor part files (`.ipt`).
  - **`Reference_Models`** – Contains ...
- **`20_Molding`** – Contains CAD files related to the manufacturing of molds for the robot.
- **`80_Experiments`** – Contains ...

# CAD Source

Dieser Ordner enthält die CAD-Quelldateien für verschiedene Roboter.

Die Roboter verwenden teilweise **gemeinsame bzw. modulare Komponenten** und teilweise **roboterspezifische Komponenten**. Deshalb sind allgemein verwendbare Bauteile von den Dateien der einzelnen Roboter getrennt.

## Ordnerstruktur

### `10_Common_Candidates`

Enthält Komponenten, die nicht eindeutig nur einem einzelnen Roboter zugeordnet sind bzw. potenziell von mehreren Robotern verwendet werden.

* `CAD_imports/` – importierte CAD-Modelle, z. B. Roboter oder Servos
* `Parts/` – allgemeine bzw. gemeinsam verwendbare Bauteile

### `20_Robots`

Enthält die CAD-Dateien der einzelnen Roboter.

Jeder Roboter ist soweit möglich nach demselben Schema aufgebaut:

* `00_Documentation/` – Dokumentation zum Roboter (z.B Stücklisten, Fertigungsanleitungen)
* `10_Product/`

  * `Assemblies/` – Baugruppen
  * `Parts/` – Bauteile des Roboters, die aus dem 3D-Drucker kommen
  * `Reference_Models/` – CAD Modelle von Teilen, die nicht mit dem 3D-Drucker gefertigt werden aber für das erstellen von Baugruppen wichtig sind
* `20_Molding/` – Gussformen


## Enthaltene Roboter

### `RL_Robot_introductions-to-robotics`

Roboter für die Lehrveranstaltung "introductions to robotics".

---

### `RR_Robot_retractable`

Längenveränderbarer Roboter.


---

### `RT6_Robot_tapered-6strings`

Tapered Robot mit sechs Seilen.


## CAD-Exports

STL- und andere Exportdateien werden nicht dauerhaft zwischen den CAD-Quelldateien abgelegt.

Die Inventor-Dateien in `10_CAD_Source` sind die Grundlage für neue Exporte.
