# BEKKSOPS
Dette er en liten readme for bekksops med formål om å forenkle prosessen av å oppdatere til nyeste versjon av sops om det trengs.
(Veldig basic, men kanskje det gjør det lettere for noen <3)

## Hente ned de nyeste endringene i getsops/sops
Sett getsops som remote upstream
```shell
git remote add upstream git@github.com:getsops/sops.git
```

Dersom du vil dobbeltsjekke at du har riktig upstream kan du sjekke dette
```shell
git remote -v 

# da vil det se ca. sånn ut
origin	git@github.com:bekk/sops.git (fetch)
origin	git@github.com:bekk/sops.git (push)
upstream	git@github.com:getsops/sops.git (fetch)
upstream	git@github.com:getsops/sops.git (push)
```

For å merge inn endringer fra getsops/main kan du kjøre følgende
```shell
# sjekk ut til ny branch og lag PR istedenfor å merge direkte på main 🤓
git checkout -b update-to-new-version-of-sops

git merge upstream/main
```

😎_lifehack på merge med getsops_😎 (per 18.mars 2025)
Endringene i dette repoet differ sjeldent med getsops sine endringer.
1. Det du må være obs på når du merger er om noe som angår _gcp\_kms_ og environment variablen 'GOOGLE\_OAUTH\_ACCESS\_TOKEN' endrer seg. Da kan det være greit å ha tunga rett i munnen når du velger hva du vil ha inn i koden din.
2. En annen ting å sjekke at blir riktig er i makefilen, hvor disse feltene settes ```PROJECT := github.com/bekk/sops```, og ```GITHUB_REPOSITORY ?= github.com/bekk/sops```.

## Test at endringene dine fungerer
1. Kjør tester
```shell
make
```
2. Du kan også dobbeltsjekke at funksjonaliteten du trenger fungerer
```shell
cd cmd/sops

go build .

# lag deg en yaml-tøysefil som kan krypteres
echo "hei: sveis" >> somefile.yaml

# logg deg inn i gcloud hvor du har tilgang på en gcp kms-nøkkel
gcloud auth login

# krypter filen 
./sops encrypt --gcp-kms <ref til din kms-nøkkel> somefile.yaml > encrypted_file.yaml && cat encrypted_file.yaml

# dekrypter filen
./sops decrypt encrypted_file.yaml
```


# Bygg nye versjoner for release
Når vi har bygget en ny release har vi gjort dette manuelt til nå, gjør gjerne noe med det om du har mulighet 💖

Det er lagt til to bekk-funksjoner i Makefilen som bygger varianter for ulike arkitekturer og OS-er.

Denne bygger sops for linux(arm64 og amd64) og darwin(arm64 og amd64)
```shell
make bekk-build BINARY_PREFIX=sops-<din versjon>

# rydd opp etterpå med
make bekk-clean
```
