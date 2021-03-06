# boinc-autodock-vina

This application works with ZIP archive that contains data to be calculated. Input ZIP archive must contain JSON file that describes the input data and other files that are used in docking process and described in the input JSON file.

Only PBDQT and MAP files are supported. If receptors and/or ligands are in different format, they should be prepared first. To do this, `prepare_ligands` and/or `prepare_receptors` sections should be specified in JSON file.

If `prepare_receptors` section contains multiple receptors, `work unit generator` will generate one work unit for each receptor; all data from other sections will be used in every generated work unit.

## JSON schema

This file supports next parameters:

- `receptor` - list of paths to receptors to be processed. This is a **optional** `list of strings` parameter. Supported file types include PDB, MOL2, PDBQ, PDBQS, PDBQT, possibly PQR, CIF. This file should not have absolute path. Can't be used together with `maps` parameter. Either `receptor` or `maps` **should be specified** for `vina` and `vinardo` scoring function. This parameter is **not allowed** for `ad4` scoring function.
- `repair` - type of repairs to make, this is an **optional** `string` parameter:
    - `bonds_hydrogens`: build bonds and add hydrogens
    - `bonds`: build a single bond from each atom with no bonds to its closest neighbor
    - `hydrogens`: add hydrogens
    - `checkhydrogens`: add hydrogens only if there are none already
    - `None`: do not make any repairs (default)
- `preserves` - preserve input charges on specific atom types, e.g. `Zn`, `Fe`. Passing `all` will preserve all input charges and do not add new charges (default is addition of gasteiger charges). Default is 'nphs_lps_waters_nonstdres'. This is an optional **list of strings** parameter.
- `cleanup` - cleanup type:
    - `nphs`: merge charges and remove non-polar hydrogens
    - `lps`: merge charges and remove lone pairs
    - `waters`: remove water residues
    - `nonstdres`: remove chains composed entirely of residues of types other than the standard 20 amino acids
    - `deleteAltB`: remove XX@B atoms and rename XX@A atoms->XX
- `delete_nonstd_residue` - delete every nonstd residue from any chain. This is an optional **boolean** parameter. Default is **false**.
- `ligand` - list of paths to ligands to be processed. This is an **optional** `list of strings` parameter. Either `ligands` or `batch` parameter should be specified.
- `selected_ligands` - if `ligand` parameter refers to the file that contains more than 1 ligand, this parameter should include list of ligands that should be processed and used to generate work units. This is an **optional** `list of strings` parameter.
- `multimol` - if `ligand` parameter refers to the file that contains more than 1 ligand, for correct processing this parameter should be set to `true`. This is an optional **bool** parameter with default value set to **false**.
- `multimol_prefix` - if `ligand` parameter refers to the file that contains more than 1 ligand, replace internal molecule name in multi-molecule input by specified prefix. This is an **optional** `string` parameter.
- `break_macrocycle` - break macrocycle for docking. This is an **optional** `boolean` parameter. Default is **false**.
- `hydrate` - add water molecules for hydrated docking. This is an **optional** `boolean` parameter. Default is **false**.
- `keep_nonpolar_hydrogens` - keep non-polar hydrogens. This is an **optional** `boolean` parameter. Default is **false**: merge onto heavy atom.
- `flex` - prepare as flexible protein residue. This is an **optional** `boolean` parameter. Default is **false**.
- `rigidity_bonds_smarts` - SMARTS patterns to rigidify bonds. This is an **optional** `list of strings` parameter.
- `rigidity_bonds_indices` - indices of two atoms (in the SMARTS) that define a bond (start at 1). This is an **optional** `list of integers` parameter.
- `flexible_amides` - allow amide bonds to rotate and be non-planar, which is bad. This is an **optional** `boolean` parameter. Default is **false**.
- `double_bond_penalty` - penalty > 100 prevents breaking double bonds. This is an **optional** `double` parameter.
- `remove_index_map` - do not write map of atom indices from input to PDBQT. This is an **optional** `boolean` parameter. Default is **false**.
- `remove_smiles` - do not write smiles as remark to PDBQT. This is an **optional** `boolean` parameter. Default is **false**.
- `flex` - path to PDBQT file with flexible side chains, if any. This file should not have absolute path. This is an **optional** `string` parameter for `vina` and `vinardo` scoring functions. This parameter is **required** for `ad4` scoring function.
- `ligands` - paths to PDBQT files with ligands. This file should not have absolute path. This is an **optional** `list of string` parameters. Either `ligands` or `batch` parameter should be specified.
- `batch` - paths to PDBQT files with batch ligands. This file should not have absolute path. This is an **optional** `list of string` parameters. Either `input.ligands` or `input.batch` parameter should be specified.
- `scoring` - scoring function (`ad4`, `vina` or `vinardo`). This is an **optional** `string` parameter. Default function is `vina`.
- `maps` - path to the folder with affinity maps **including prefix**. This is an **optional** `string` parameter. E.g. for the folder with maps `.\maps\1iep_receptor.A.map` and `.\maps\1iep_receptor.C.map` should be provided as `maps\1iep_receptor`. If this parameter is not specified, then `center_x`, `center_y`, `center_z` and `size_x`, `size_y`, `size_x` parameters should be specified to generate affinity maps. Can't be used together with `receptor` parameter. Either `receptor` or `maps` **should be specified** for `vina` and `vinardo` scoring function. This parameter is **required** for `ad4` scoring function.
- `center_x` - X coordinate of the center (Angstrom). This `double` parameter is ignored when `maps` parameter is specified.
- `center_y` - Y coordinate of the center (Angstrom). This `double` parameter is ignored when `maps` parameter is specified.
- `center_z` - Z coordinate of the center (Angstrom). This `double` parameter is ignored when `maps` parameter is specified.
- `size_x` - size in the X dimension (Angstrom). This `double` parameter is ignored when `maps` parameter is specified.
- `size_y` - size in the Y dimension (Angstrom). This `double` parameter is ignored when `maps` parameter is specified.
- `size_z` - size in the Z dimension (Angstrom). This `double` parameter is ignored when `maps` parameter is specified.
- `autobox` - sets maps dimensions based on input ligand(s). This parameter is used when `score_only` or `local_only` parameter is specified. This is an **optional** `boolean` parameter. Default value is `false`.
- `out` - path to output model file (PDBQT). This file should not have absolute path. This is an **optional** parameter.
- `dir` - path to output directory when: (1) in batch mode, (2) `ligands` parameter is specified and contains more than 1 file and `score_only` is not specified. This directory should not have absolute path. This is an **optional** `string` parameter.
- `write_maps` - output filename (directory + prefix name) for maps. Parameter `force_even_voxels` may be needed to comply with map format. This is an **optional** `string` parameter. E.g. for the folder with maps `.\maps\1iep_receptor.A.map` and `.\maps\1iep_receptor.C.map` should be provided as `maps\1iep_receptor`.
- `score_only` - search space can be omitted. This is an **optional** `boolean` parameter. Default value is `false`.
- `local_only` - do local search only. This is an **optional** `boolean` parameter. Default value is `false`.
- `no_refine` - when `receptor` is provided, do not use explicit receptor atoms (instead of precalculated grids) for: (1) local optimization and scoring after docking, (2) `local_only` jobs, and (3) `score_only` jobs. This is an **optional** `boolean` parameter. Default value is `false`.
- `force_even_voxels` - calculated grid maps will have an even number of voxels (intervals) in each dimension (odd number of grid points). This is an **optional** `boolean` parameter. Default value is `false`.
- `randomize_only` - randomize input, attempting to avoid clashes. This is an **optional** `boolean` parameter. Default value is `false`.
- `weight_glue` - macrocycle glue weight. This is an optional `double` parameter. Default value is `50.000000`.
- `seed` - explicit random seed. This is an **optional** `integer` parameter. Default value is `0`.
- `exhaustiveness` - exhaustiveness of the global search (roughly proportional to time): 1+. This is an **optional** `integer` parameter. Default value is `8`.
- `max_evals` - number of evaluations in each MC run (if zero the number of MC steps is based on heuristics). This is an **optional** `integer` parameter. Default value is `0`.
- `num_modes` - maximum number of binding modes to generate. This is an **optional** `integer` parameter. Default value is `9`.
- `min_rmsd` - minimum RMSD between output poses. This is an **optional** `double` parameter. Default value is `1.0`.
- `energy_range` - maximum energy difference between the best binding mode and the worst one displayed (kcal/mol). This is an **optional** `double` parameter. Default value is `3.0`.
- `spacing` - grid spacing (Angstrom). This is an **optional** `double` parameter. Default value is `0.375`.

`vina` scoring function specific parameters.

- `weight_gauss1` - gauss_1 weight. This is an **optional** `double` parameter. Default value is `-0.035579`.
- `weight_gauss2` - gauss_2 weight. This is an **optional** `double` parameter. Default value is `-0.005156`.
- `weight_repulsion` - repulsion weight. This is an **optional** `double` parameter. Default value is `0.840245`.
- `weight_hydrophobic` - hydrophobic weight. This is an **optional** `double` parameter. Default value is `-0.035069`.
- `weight_hydrogen` - Hydrogen bond weight. This is an **optional** `double` parameter. Default value is `-0.587439`.
- `weight_rot` - N_rot weight. This is an **optional** `double` parameter. Default value is `0.05846`.

`vinardo` scoring function specific parameters.

- `weight_vinardo_gauss1` - gauss_1 weight. This is an **optional** `double` parameter. Default value is `-0.045`.
- `weight_vinardo_repulsion` - repulsion weight. This is an **optional** `double` parameter. Default value is `0.8`.
- `weight_vinardo_hydrophobic` - hydrophobic weight. This is an **optional** `double` parameter. Default value is `-0.035`.
- `weight_vinardo_hydrogen` - Hydrogen bond weight. This is an **optional** `double` parameter. Default value is `0.600`.
- `weight_vinardo_rot` - N_rot weight. This is an **optional** `double` parameter. Default value is `0.05846`.

`ad4` scoring function specific parameters.

- `weight_ad4_vdw` - ad4_vdw weight. This is an **optional** `double` parameter. Default value is `0.1662`.
- `weight_ad4_hb` - ad4_hb weight. This is an **optional** `double` parameter. Default value is `0.1209`.
- `weight_ad4_elec` - ad4_elec weight. This is an **optional** `double` parameter. Default value is `0.1406`.
- `weight_ad4_dsolv` - ad4_dsolv weight. This is an **optional** `double` parameter. Default value is `0.1322`.
- `weight_ad4_rot` - ad4_rot weight. This is an **optional** `double` parameter. Default value is `0.2983`.

More information about specified parameters could be found on the official documentation webpage of Autodock-Vina: https://autodock-vina.readthedocs.io/en/latest/index.html

## Examples of JSON files with already prepared receptors and ligands

### Minimal JSON file

```json
{
    "receptors": "1iep_receptor.pdbqt",
    "ligands": "1iep_ligand.pdbqt"
}
```

### JSON file with search area, exhaustiveness and output specified

```json
{
    "receptors": "1iep_receptor.pdbqt",
    "ligands": "1iep_ligand.pdbqt",
    "center_x": 15.190,
    "center_y": 53.903,
    "center_z": 16.917,
    "size_x": 20.0,
    "size_y": 20.0,
    "size_z": 20.0,
    "exhaustiveness": 4,
    "out": "1iep_ligand_vina_out.pdbqt"
}
```
### JSON file with two receptors and one ligand with with search area and exhaustiveness specified

```json
{
    "receptors": [
        "1iep_receptor.pdb",
        "1iep_receptor_copy.pdb"
    ],
    "ligands": [
        "1iep_ligand.sdf",
        "1iep_ligand_copy.sdf"
    ],
    "center_x": 15.190,
    "center_y": 53.903,
    "center_z": 16.917,
    "size_x": 20.0,
    "size_y": 20.0,
    "size_z": 20.0,
    "exhaustiveness": 4
}
```

From this file `work unit generator` will generate **four** work units:
- first work unit with `1iep_receptor.pdb` and `1iep_ligand.sdf`
- second work unit with `1iep_receptor.pdb` and `1iep_ligand_copy.sdf`
- third work unit with `1iep_receptor_copy.pdb` and `1iep_ligand.sdf`
- fourth work unit with `1iep_receptor_copy.pdb` and `1iep_ligand_copy.sdf`
