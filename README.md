# Segmentation automatique d'IRM cardiaque préclinique par intelligence artificielle

Stage de recherche — Master 2 Mathématiques, Modélisation et Simulation (UPPA), mars–juillet 2025
**CRMBM** (Centre de Résonance Magnétique Biologique et Médicale), Aix-Marseille Université / CNRS / AP-HM

## Contexte

Le diabète de type 2 est un facteur de risque majeur de dysfonction cardiaque. L'IRM préclinique (7T / 11.7T) permet d'explorer finement la fonction cardiaque chez le rongeur, mais la segmentation manuelle du ventricule gauche sur les séries dynamiques est longue, fastidieuse et peu reproductible — un frein pour l'analyse de cohortes complètes.

## Objectifs

- Automatiser la segmentation du ventricule gauche (VG) sur IRM cardiaque **2D+t** et **3D+t** à l'aide de **nnU-Net**.
- Générer des courbes volume-temps pour analyser la fonction diastolique.
- Évaluer la robustesse et la généralisation du modèle entre espèces (souris → rat).

## Données

- **CardiaTeam** : cohorte de souris avec diabète induit.
- **Tollsome** : cohorte de souris génétiquement modifiées.
- **Rats** : cohorte indépendante utilisée pour tester la généralisation inter-espèces.
- 376 images 2D+t annotées manuellement (30 frames chacune) et 69 volumes 3D+t (9 coupes, 30 frames), annotés sous **FSLeyes** (label 1 = VG interne, label 2 = myocarde externe).

## Méthodologie

Pipeline complet, développé et exécuté au fil du stage (structure chronologique conservée dans `notebooks/`, mois par mois) :

1. **Prétraitement** — conversion et renommage des fichiers (DICOM → NIfTI, `.nii` → `.nii.gz`), classification des séries.
2. **Annotation manuelle** — masques de référence sous FSLeyes.
3. **Entraînement nnU-Net** — mode 2D, patchs 128×128, `--fold all`, exécuté sur le **Mésocentre AMU** (cluster HPC, SLURM + GPU).
4. **Évaluation** — métriques Dice, HD95, ASSD, Jaccard, Precision, Recall, comparées entre cohortes et entre modèles (souris seul, rat seul, mixte).
5. **Extraction fonctionnelle** — calcul du volume VG frame par frame (somme des surfaces segmentées × épaisseur inter-coupe), génération automatique des courbes volume-temps et détection d'anomalies temporelles.

## Résultats

- Dice ≈ **0.97** sur les souris après réentraînement du modèle.
- Généralisation souris → rat directe insuffisante ; meilleur compromis obtenu avec un modèle mixte (souris + rats).
- Sur IRM 4D complètes (69 volumes) : Dice ≈ **0.97**, ASSD ≈ **0.1 mm**, HD95 **< 1 mm**.

## Stack technique

Python · nnU-Net · calcul HPC (SLURM, Mésocentre AMU) · FSLeyes · Jupyter / Google Colab

## Structure du repo

```
notebooks/
  mars/        # premiers scripts : exploration, extraction d'infos, scripts par groupe d'images
  avril/       # conversion NIfTI, classification, préparation 4D, entraînement nnU-Net, visualisation
  mai/         # comparaison de résultats de prédiction, extraction de descripteurs, segmentation rats
  juillet/     # préparation de données finale, entraînement
docs/
  Soutenance_stage_CRMBM.pptx        # support de soutenance de stage
  Presentation-ED659-concours.pdf    # présentation concours ED659 (contexte académique + résultats)
```

Les notebooks sont conservés dans leur organisation chronologique d'origine (dossiers datés) : elle reflète la progression réelle du travail au long du stage, de l'exploration initiale jusqu'au pipeline final validé sur IRM 4D. Les sorties de cellules (images, logs) ont été nettoyées pour alléger le dépôt ; le code reste inchangé.

## Encadrement

- **Dr. Frank Kober** — CRMBM, équipe CardioVasculaire
- **Dr. Emilien Royer** — CRMBM, équipe CardioVasculaire

Stage financé par l'**Institut Marseille Imaging**.

## Auteure

Amina Menacer — Master 2 Mathématiques, Modélisation et Simulation, Université de Pau et des Pays de l'Adour (UPPA)
