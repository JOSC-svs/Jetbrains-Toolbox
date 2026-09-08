# Jetbrains-Toolbox
# JetBrains Toolbox för Apple Silicon via AutoPkg och Munki

Lösningen består av två recept:

- `JetBrainsToolbox-arm64.download.recipe.yaml` hittar och hämtar senaste stabila Apple Silicon-versionen samt verifierar JetBrains kodsignatur.
- `JetBrainsToolbox-arm64.munki.recipe.yaml` ärver download-receptet och innehåller all Munki-specifik information.

## Lägg in recepten

Båda filerna måste ligga i en katalog som finns i AutoPkgs `RECIPE_SEARCH_DIRS`:

```bash
mkdir -p ~/Library/AutoPkg/Recipes/JetBrainsToolbox
cp JetBrainsToolbox-arm64.*.recipe.yaml ~/Library/AutoPkg/Recipes/JetBrainsToolbox/
```

Kontrollera att AutoPkg kan hitta relationen mellan recepten:

```bash
autopkg info JetBrainsToolbox-arm64.download.recipe.yaml
autopkg info JetBrainsToolbox-arm64.munki.recipe.yaml
```

## Testa endast nedladdningen

```bash
autopkg run JetBrainsToolbox-arm64.download.recipe.yaml -vv
```

Detta importerar ingenting till Munki.

## Anpassa Munki-informationen

Öppna `JetBrainsToolbox-arm64.munki.recipe.yaml`. Under `Input` kan du ändra:

```yaml
Input:
  NAME: JetBrainsToolbox
  MUNKI_REPO_SUBDIR: apps/JetBrainsToolbox
  MUNKI_CATALOG: testing
  MUNKI_CATEGORY: Developer Tools
  MUNKI_DEVELOPER: JetBrains
  MUNKI_DESCRIPTION: Manages installations and updates of JetBrains IDEs.
  MUNKI_DISPLAY_NAME: JetBrains Toolbox
```

Ytterligare Munki-nycklar kan läggas under `pkginfo`. Exempel:

```yaml
      pkginfo:
        minimum_os_version: "13.0"
        unattended_uninstall: true
```

Behåll följande arkitekturbegränsning så att paketet inte erbjuds till Intel-datorer:

```yaml
        supported_architectures:
          - arm64
```

## Importera till Munki

```bash
autopkg run JetBrainsToolbox-arm64.munki.recipe.yaml -vv
```

Som standard får Munki-posten namnet `JetBrainsToolbox`, hamnar under `apps/JetBrainsToolbox` och läggs i katalogen `testing`.

Receptet markerar `JetBrains Toolbox` som blockerande process. En obevakad uppdatering skjuts därför upp om appen körs.

När paketet är testat lägger du `JetBrainsToolbox` i rätt manifest och inkluderar posten i er produktionskatalog enligt ert vanliga Munki-flöde.
