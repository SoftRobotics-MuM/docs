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
