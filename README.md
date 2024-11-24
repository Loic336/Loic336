// ==Script utilisateur==
// @name Générateur de code Fortnite
// @namespace https://spin.tk
// @licence GNU GPLv3
// @version 1.1
// @description Malgré le nom un peu flou, il s'agit d'un véritable générateur de code. Les chances d'obtenir un code réel sont faibles mais pas nulles.
// @auteur Spinfal
// @match https://www.epicgames.com/store/*
// @match https://www.epicgames.com/id/login*
// @icon https://www.google.com/s2/favicons?domain=epicgames.com
// @grant aucun
// @run-at démarrage-du-document
// @downloadURL https://update.greasyfork.org/scripts/437627/Fortnite%20Code%20Generator.user.js
// @updateURL https://update.greasyfork.org/scripts/437627/Fortnite%20Code%20Generator.meta.js
// ==/Script utilisateur==

(fonction() {
    'utiliser strict';

    const contrôleur = nouveau AbortController();
    const signal = contrôleur.signal;
    var en cours d'exécution = faux;
    si (sessionStorage['erreur'] !== indéfini) {
        alerte(sessionStorage['erreur']);
        sessionStorage.removeItem('erreur');
    }
    const Style = {
        base: [
            "couleur: #2ba9a9",
            "taille de police : 30 px",
            "couleur d'arrière-plan : #444",
            "rembourrage : 2px 4px",
            « rayon de bordure : 2 px »
        ],
        secondaire : [
            "taille de police : 15 px",
            "couleur: #eee",
            « couleur d'arrière-plan : #2b2b2b »
        ],
        succès: [
            "taille de police : 25 px",
            "couleur: vert",
            « couleur d'arrière-plan : #2b2b2b »
        ],
        erreur: [
            "taille de police : 25 px",
            "couleur : rouge",
            « couleur d'arrière-plan : #2b2b2b »
        ],
    }
    const log = (texte, extra = []) => {
        laissez style = Style.base.join(';') + ';';
        style += extra.join(';'); // Ajouter des styles supplémentaires
        console.log(`%c${texte}`, style);
    }

    log("Veuillez patienter pendant qu'Epic a fini de spammer la console avec cette merde");
    fenêtre.onload = () => {
        setImmediate(() => {
            const div = document.createElement('div');
            div.setAttribute('style', 'background-color: #0078F2; color: white; width: 16vh; border-radius: 4px; padding: 5px; cursor: default;');
            div.innerHTML = '<p>Ouvrez la console du développeur pour le générateur de code</p>';
            document.querySelector('[data-component=SiteNav]').appendChild(div);
            window.onkeydown = (événement) => {
                console.effacer();
                si (event.key === '/' && en cours d'exécution === faux) {
                    log("Démarrage du générateur...", Style.secondary);
                    setInterval(() => checkCodes(), 1000);
                } sinon si (en cours d'exécution) {
                    log("Le générateur est déjà en cours d'exécution !", Style.secondary);
                }
            }
            console.effacer();
            log("Générateur de code Fortnite");
            log("Pour exécuter le générateur, appuyez sur la touche / de votre clavier ! (pas dans la console)\nPour arrêter le générateur, rechargez simplement votre page.", Style.secondary);
        });
    }

    fonction makecode(longueur) {
        var résultat = '';
        var caractères = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
        var charactersLength = caractères.length;
        pour ( var i = 0; i < longueur; i++ ) {
            résultat += caractères.charAt(Math.floor(Math.random() *
                                                   caractèresLongueur));
        }
        retourner le résultat;
    }

    // res.json()).then(res => JSON.stringify(res)).then(res =>
    fonction checkCodes() {
        en cours d'exécution = vrai;
        const codecadeau = makecode(16);

        récupérer(`https://www.epicgames.com/fortnite/en-US/api/posa/${giftCode}/status`, {
            signal: signal,
            « en-têtes » : {
                "accepter": "application/json, texte/plain, */*",
                "accept-language": "en-US,en;q=0.9",
                "sec-ch-ua": "\"Chromium\";v=\"96\", \"Google Chrome\";v=\"82\", \";Pas une marque\";v=\"99\"",
                "sec-ch-ua-mobile": "?0",
                "sec-ch-ua-platform": "\"Linux\"",
                "sec-fetch-dest": "vide",
                "sec-fetch-mode": "cors",
                "sec-fetch-site": "même-origine",
                "x-requested-with": "XMLHttpRequest"
            },
            "référent": "https://www.epicgames.com/fortnite/en-US/vbuckscard?fromCta=true",
            "referrerPolicy": "origine-stricte-lors-d'origine-croisée",
            "corps": nul,
            "méthode": "GET",
            "mode": "cors",
            « informations d'identification » : « inclure »
        }).then(res => {
            commutateur (res?.status) {
                cas 200:
                    log(giftCode + " est un code valide", Style.success);
                    casser;
                cas 404:
                    log(giftCode + " n'est pas un code valide", Style.error);
                    casser;
                cas 401:
                    contrôleur.abort();
                    emplacement.reload();
                    sessionStorage['error'] = 'vous n'êtes pas connecté à un compte epicgames !';
                    //alert("vous n'êtes pas connecté à un compte epicgames !");
                    fenêtre.open('https://www.epicgames.com/id/login', '_self');
                    casser;
                cas 429:
                    contrôleur.abort();
                    emplacement.reload();
                    sessionStorage['error'] = 'votre débit est limité, réessayez plus tard';
                    //alert("votre débit est limité, réessayez plus tard");
                    casser;
                défaut:
                    en cours d'exécution = faux;
                    contrôleur.abort();
                    log("une erreur s'est produite avec le code d'état : " + res?.status);
            }
        }).catch(console.erreur);
    }
})();
