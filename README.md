# Sunshine + débit adaptatif

Fork de [LizardByte/Sunshine](https://github.com/LizardByte/Sunshine) qui ajoute un **débit vidéo adaptatif** :
Sunshine baisse le débit de l'encodeur NVENC quand Moonlight signale des pertes de paquets, et le remonte
par paliers quand la liaison redevient propre, **sans interrompre le stream** (l'encodeur est reconfiguré à chaud).

Cette branche `automatisation` ne contient que l'outillage :

- `patches/0001-*.patch` : la modification elle-même (candidate à une proposition au projet Sunshine) ;
- `patches/0002-*.patch` : le workflow de compilation du fork (`build-adaptatif.yml`), qui réutilise la recette
  Windows officielle `ci-windows.yml` de la version compilée ;
- `.github/workflows/build-adaptatif.yml` : copie du même workflow, présente ici pour que GitHub le liste.

Les branches `adaptatif-<tag>` (= version officielle + patches) sont créées et poussées depuis un PC avec
GitHub CLI (`sync_adaptatif.ps1`, chaque semaine), car le jeton automatique des Actions n'a pas le droit de
pousser un fichier de workflow. La compilation est lancée sur cette branche (`gh workflow run build-adaptatif.yml
--ref adaptatif-<tag>`) et l'installeur est publié dans la release `<tag>-adaptatif`. En cas d'échec,
notification ntfy (secret `NTFY_TOPIC`).

## Options ajoutées à `sunshine.conf`

| Clé | Défaut | Rôle |
| --- | --- | --- |
| `adaptive_bitrate` | `disabled` | Active le régulateur (NVENC seulement). |
| `adaptive_bitrate_min_percent` | `20` | Plancher, en pourcentage du débit demandé par Moonlight (5-100). |
| `adaptive_bitrate_decrease_percent` | `25` | Baisse appliquée à chaque seconde avec pertes (5-75). |
| `adaptive_bitrate_increase_delay` | `8000` | Temps sans perte, en ms, avant de remonter de 15 % (1000-60000). Double après chaque remontée suivie de pertes, jusqu'à 60 s. |

## Fonctionnement

Moonlight envoie déjà à Sunshine un état FEC par image abîmée (`SS_FRAME_FEC_STATUS`, type 0x5502 : paquets
perdus, reçus, parité) ainsi que des demandes d'image clé ou d'invalidation d'images quand il perd une image.
Ces signaux alimentent un régulateur AIMD par session ; la nouvelle cible passe à la boucle d'encodage par un
événement, et le backend NVENC l'applique avec `NvEncReconfigureEncoder()` (paramètres de débit seulement :
pas de réinitialisation, pas d'image clé forcée), après avoir vérifié la capacité `NV_ENC_CAPS_SUPPORT_DYN_BITRATE_CHANGE`.
