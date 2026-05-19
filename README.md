# scan-sast-sonar-semgrep-all-repos-organisation en local
Ceci est un projet qui permet de faire le scan de vulnerabilite d une organisation avec les version communities de sonar et semgrep


Ceci est un projet adapter a un scan sur plusieurs repository en une seule fois dans une organisation/entreprise

## Mise place de l'environnement

### 1. Installer et lancer Sonar en local avec Docker

#### installer docker
```
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker


#Vérifier

docker --version

#lancer sonar

sudo docker run -d --name sonarqube \
  -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
  -p 9000:9000 \
  sonarqube:latest
```

### 2.Installer et lancer Semgrep
```
# 1. Créer le venv
python3 -m venv semgrep-env

# 2. Activer le venv
source semgrep-env/bin/activate

# 3. Installer semgrep
pip install semgrep

# 4. Vérifier l'installation
semgrep --version
```

### 3. Cloner le repos de l'organisation/entreprise 
1. configurer et executer`clone_all_repos.sh`

GITHUB_ORG="nom_organisation"
GITHUB_TOKEN="votre_token_github" # generer dans settings > developper settings > personal access tokens > tokens > generate new token
WORK_DIR="/home/kali/Documents/SAST/repos"

2. executer`clone_all_repos.sh`

```
chmod +x clone_all_repos.sh

./clone_all_repos.sh


```

## Configuration des scripts

### 1. sonar

- Recuperer la liste des repositories/projets et remplacer '/repos/downloaded/directory' par le dossier des repos telecharger :

`nano generate_repos_list.sh`

puis
```
chmod +x generate_repos_list.sh

./generate_repos_list.sh

```
un fichier `repos_list.txt` est genere.

- Configurer 'scan_multi_repos.sh` :

```
SONAR_HOST_URL="http://localhost:9000" #changer si le port a changer. c'est l'url de sonar
SONAR_TOKEN="squ_...." # se rendre dans sonar My account > Security > Generate Tokens. choisir User token
REPOS=(. # remplir par la repos_list.txt
```
- lancer le scan sonar


```
chmod +x scan_multi_repos.sh

./scan_multi_repos.sh

```
- recuperation du rapport en csv dans le cas ou il n'a pas charge :

#### installer sonnar-tools

 1. Créer le venv
`python3 -m venv sonar-venv`

 2. L'activer
 Sur Linux :
`source sonar-venv/bin/activate`

 3. Mettre à jour pip
`pip install --upgrade pip`

 4. Installer sonar-tools
`pip install sonar-tools`

#### lancer sonnar-tools


1. de facon glogal : methode 1

```
chmod +x sonar_full_export.sh

./sonar_full_export.sh

```

2. de facon controler : methode 2

- Configurer 'export_vulns.sh` :

```
SONAR_HOST_URL="http://localhost:9000" #changer si le port a changer. c'est l'url de sonar
SONAR_TOKEN="squ_...." # se rendre dans sonar My account > Security > Generate Tokens. choisir User token ou recopier l ancien token
VENV egalement
```
Il faudra recup la liste des projets dans sonar avec la `gen-list-proj.sh`

- lancer le la recup


```
chmod +x export_vulns.sh

./export_vulns.sh

```

- pour combiner les rapports, lancer `merge.sh`

### semgrep

configurer : 
- REPOS_BASE = /repos/downloaded/directory
- PROJECT_FILE = utilise le fichier generer par
`detect_lang_and_scan_semgrep.sh`
```
chmod +x detect_lang_and_scan_semgrep.sh

./detect_lang_and_scan_semgrep.sh

```




note je dois combiner la recuperation du rapport. obtenir en sortis ne donne qu un fichier csv

dans la perspective d evolution, je dois aboutir au lancement des deux scan et recup des rapport en un seul lancement de script
