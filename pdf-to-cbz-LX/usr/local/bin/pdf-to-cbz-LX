#!/bin/bash
# ==============================================================================
# Script Name : PDF to CBZ Converter (with YAD interface)
# Author      : dpmw (https://github.com/pacman29800)
# License     : MIT License
#
# MIT License
# 
# Copyright (c) 2025 dpmw
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.
# ==============================================================================


# ✅ Vérification des dépendances
for cmd in yad pdftoppm pdfinfo zip; do
    if ! command -v "$cmd" &>/dev/null; then
        echo "Erreur : '$cmd' n'est pas installé. Installe-le d'abord."
        exit 1
    fi
done

# ✅ Paramètres par défaut
DELETE_ORIGINAL="TRUE"
RESOLUTION="200"
IMG_FORMAT="jpeg"
OUTPUT_DIR="$HOME"

# ✅ Fenêtre avec formulaire + commentaires
OPTIONS=$(yad --form --title="Conversion PDF vers CBZ" --width=600 --center \
    --field="Supprimer les PDF originaux:CHK" "$DELETE_ORIGINAL" \
    --field="Résolution (DPI):NUM" "$RESOLUTION!100..600!50" \
    --field="Format image:CB" 'jpeg!png!tiff' \
    --field="Répertoire de sortie:DIR" "$OUTPUT_DIR" \
    --text="
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
🛈 *Conseils d'utilisation :*

- *** Résolution (DPI) :
     150 DPI = léger mais parfois flou,
     200 DPI = bon compromis,
     300+ DPI = meilleure qualité mais fichiers plus lourds.
- *** Format image :
     JPEG = léger, compression avec perte (recommandé pour BD couleur),
     PNG = sans perte, idéal pour BD noir et blanc ou archives de qualité,
     TIFF = très lourd, usage rare.

- *** Recommandations :
- BD couleurs classiques → ✅ JPEG
- BD noir et blanc, ligne claire → ✅ PNG
- Archivage longue durée → ✅ PNG
- Lecture sur mobile / liseuse → ✅ JPEG

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
" \
    --buttons-layout=center)

# ✅ Sortie si annulation
[[ -z "$OPTIONS" ]] && exit 0

# ✅ Récupération des choix
DELETE_ORIGINAL=$(echo "$OPTIONS" | cut -d'|' -f1)
RESOLUTION=$(echo "$OPTIONS" | cut -d'|' -f2)
IMG_FORMAT=$(echo "$OPTIONS" | cut -d'|' -f3)
OUTPUT_DIR=$(echo "$OPTIONS" | cut -d'|' -f4)

# Convertir IMG_FORMAT en minuscule pour extension correcte
EXT="${IMG_FORMAT,,}"
[[ "$EXT" == "jpeg" ]] && EXT="jpg"

# ✅ Sélection des fichiers PDF
if [[ $# -eq 0 ]]; then
    FILES=$(yad --file --title="Sélectionnez les fichiers PDF" --file-filter="*.pdf *.PDF" --multiple --separator="|")
    [[ -z "$FILES" ]] && exit 0
    IFS="|" read -r -a FILE_ARRAY <<< "$FILES"
else
    FILE_ARRAY=("$@")
fi

TOTAL_FILES=${#FILE_ARRAY[@]}
[[ $TOTAL_FILES -eq 0 ]] && {
    yad --error --title="Erreur" --text="Aucun fichier PDF sélectionné." --center
    exit 1
}

# ✅ Temp & log
TEMPDIR_GLOBAL=$(mktemp -d)
LOGFILE=$(mktemp)
STATUS_FILE=$(mktemp)

echo "SUCCESS=0" > "$STATUS_FILE"
echo "FAILED=0" >> "$STATUS_FILE"
echo "FAILED_FILES=()" >> "$STATUS_FILE"

# ✅ Nombre de pages par PDF
get_page_count() {
    pdfinfo "$1" 2>/dev/null | awk '/^Pages:/ {print $2}'
}

TOTAL_PAGES=0
declare -A PAGES_PER_FILE

for f in "${FILE_ARRAY[@]}"; do
    pages=$(get_page_count "$f")
    [[ -z "$pages" ]] && pages=0
    PAGES_PER_FILE["$f"]=$pages
    TOTAL_PAGES=$((TOTAL_PAGES + pages))
done

[[ $TOTAL_PAGES -eq 0 ]] && {
    yad --error --title="Erreur" --text="Impossible de déterminer le nombre total de pages." --center
    exit 1
}

PROGRESS=0

# ✅ Conversion
(
    for f in "${FILE_ARRAY[@]}"; do
        BASENAME="$(basename "$f" .pdf)"
        pages=${PAGES_PER_FILE["$f"]}
        DEST_DIR="$TEMPDIR_GLOBAL/$BASENAME"
        mkdir -p "$DEST_DIR"
        conversion_failed=0

        echo "# Conversion de $BASENAME..."

        for ((i=1; i<=pages; i++)); do
            # Conversion PDF -> image page par page
            pdftoppm -${IMG_FORMAT} -r "$RESOLUTION" -f "$i" -l "$i" "$f" "$DEST_DIR/page-$(printf '%04d' "$i")" &>> "$LOGFILE"
            if [[ $? -ne 0 ]]; then
                echo "Erreur conversion page $i de $BASENAME" >> "$LOGFILE"
                conversion_failed=1
                break
            fi
            ((PROGRESS++))
            PERCENT=$((100 * PROGRESS / TOTAL_PAGES))
            echo "$PERCENT"
            echo "# $BASENAME : page $i / $pages"
        done

        if [[ $conversion_failed -eq 1 ]]; then
            source "$STATUS_FILE"
            ((FAILED++))
            FAILED_FILES+=("$BASENAME")
            echo "SUCCESS=$SUCCESS" > "$STATUS_FILE"
            echo "FAILED=$FAILED" >> "$STATUS_FILE"
            echo "FAILED_FILES=(${FAILED_FILES[@]})" >> "$STATUS_FILE"
            rm -rf "$DEST_DIR"
            continue
        fi

        OUTPUT_CBZ="$OUTPUT_DIR/${BASENAME}.cbz"

        # Si le fichier CBZ existe déjà
        if [[ -f "$OUTPUT_CBZ" ]]; then
            CHOICE=$(yad --list --title="Fichier existe déjà" \
                --text="Le fichier $OUTPUT_CBZ existe déjà. Que faire ?" \
                --column="Action" "Écraser" "Renommer" "Ignorer" --width=300 --height=200 --center)

            case "$CHOICE" in
                Écraser) rm -f "$OUTPUT_CBZ" ;;
                Renommer) OUTPUT_CBZ="${OUTPUT_CBZ%.cbz}_$(date +%s).cbz" ;;
                Ignorer|"") rm -rf "$DEST_DIR"; continue ;;
            esac
        fi

        # Compression en CBZ
        zip -j "$OUTPUT_CBZ" "$DEST_DIR"/*."$EXT" &>> "$LOGFILE"
        if [[ $? -ne 0 ]]; then
            echo "Erreur création CBZ pour $BASENAME" >> "$LOGFILE"
            source "$STATUS_FILE"
            ((FAILED++))
            FAILED_FILES+=("$BASENAME")
            echo "SUCCESS=$SUCCESS" > "$STATUS_FILE"
            echo "FAILED=$FAILED" >> "$STATUS_FILE"
            echo "FAILED_FILES=(${FAILED_FILES[@]})" >> "$STATUS_FILE"
            rm -rf "$DEST_DIR"
            continue
        fi

        # Supprimer PDF original si choisi
        if [[ "$DELETE_ORIGINAL" == "TRUE" ]]; then
            rm -f "$f"
        fi

        rm -rf "$DEST_DIR"

        source "$STATUS_FILE"
        ((SUCCESS++))
        echo "SUCCESS=$SUCCESS" > "$STATUS_FILE"
        echo "FAILED=$FAILED" >> "$STATUS_FILE"
        echo "FAILED_FILES=(${FAILED_FILES[@]})" >> "$STATUS_FILE"
    done

    echo "100"
    echo "# Conversion terminée."
) | yad --progress --title="Conversion PDF → CBZ" --auto-close --auto-kill --width=600 --center --button=Annuler:1

# ✅ Nettoyage
rm -rf "$TEMPDIR_GLOBAL"

# ✅ Résumé
source "$STATUS_FILE"
rm "$STATUS_FILE"

if [[ $SUCCESS -eq 0 && $FAILED -eq 0 ]]; then
    yad --info --title="Annulé" --text="La conversion a été annulée ou aucun fichier traité." --center
    rm -f "$LOGFILE"
    exit 0
fi

summary="✅ Succès : $SUCCESS fichier(s)\n❌ Échecs : $FAILED fichier(s)"
[[ ${#FAILED_FILES[@]} -gt 0 ]] && summary+="\n\nFichiers échoués :\n$(printf '%s\n' "${FAILED_FILES[@]}")"

yad --info --title="Bilan de conversion" --text="$summary" --width=500 --center

# ✅ Proposition de sauvegarde du log
if [[ -s "$LOGFILE" ]]; then
    yad --question --title="Sauvegarder le log ?" \
        --text="Souhaitez-vous enregistrer le fichier log ?" \
        --button="Oui:0" --button="Non:1" --center
    if [[ $? -eq 0 ]]; then
        SAVE_PATH=$(yad --file --save --title="Enregistrer le log" --filename="$HOME/conversion.log" --center)
        [[ -n "$SAVE_PATH" ]] && cp "$LOGFILE" "$SAVE_PATH"
    fi
fi

rm -f "$LOGFILE"
exit 0
