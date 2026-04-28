# lsdf-automation-modules

Repo public hébergeant les **releases binaires** (.zip) des modules PowerShell partagés utilisés par les runbooks Azure Automation de **LSDF Conseils** et **Serenity Conciergerie**.

## Pourquoi un repo public ?

Les Automation Accounts Azure ne peuvent ingérer un module custom que via une URL HTTP publique (`ContentLinkUri` dans `New-AzAutomationModule`). GitHub Releases fournit cette URL gracieusement, avec versioning natif, historique complet et rollback trivial — sans coût ni infrastructure à maintenir.

Aucun secret, credential ou information sensible n'est publié ici — uniquement le code source de bibliothèques utilitaires (rendu HTML de rapports, helpers de logging, etc.).

## Source de vérité

Le **code source** des modules vit dans le repo privé `clients-private`, à `clients/active/lsdf-conseils/automation/lib/<NomModule>/`. Ce repo public ne sert qu'à publier les `.zip` packagés via Releases.

## Modules publiés

| Module | Description | Première version |
|---|---|---|
| `LSDF.AutomationReports` | Génération de rapports HTML standardisés pour runbooks Azure Automation (frame commun + 5 briques de contenu paramétrables) | À venir |

## Workflow de publication

Le script `Publish-AACustomModule.ps1` (dans le repo privé `clients-private`) :

1. Lit le `.psd1` du module pour récupérer nom + version
2. Zippe le dossier source
3. Crée un Release `<Module>/v<Version>` avec le zip en asset (`gh release create`)
4. Récupère l'URL de l'asset
5. Importe le module dans l'Automation Account via `Invoke-AzRestMethod PUT` avec `ContentLink.uri = URL`
6. Poll `ProvisioningState` jusqu'à `Succeeded`

Les releases sont **immuables** : pour rollback, il suffit de réimporter le module depuis l'URL d'un ancien tag.

## Convention de nommage des Releases

Format : `<Module>/v<SemVer>`, ex :
- `LSDF.AutomationReports/v1.0.0`
- `LSDF.AutomationReports/v1.1.0`

Le préfixe par module permet de coexister plusieurs modules dans le même repo sans conflit de tags.
