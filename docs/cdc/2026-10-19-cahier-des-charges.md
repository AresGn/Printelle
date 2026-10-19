# Cahier des charges (PRD) – Plateforme de vérification et d'aperçu 3D de cahiers imprimés

Oct 19, 2026 · @Ares Compte

## 1. Contexte, problème et vision

Le site permet de voir un cahier agrafé avant impression et de vérifier l'ordre de ses pages, pour éviter les erreurs entre client et imprimeur.

**Le problème.** Pour un cahier agrafé, les pages ne s'impriment pas dans l'ordre de lecture. Un cahier de 16 pages s'imprime en 4 feuilles recto-verso : 16 | 1, 2 | 15, 14 | 3, 4 | 13, 12 | 5, 6 | 11, 10 | 7, 8 | 9. Les clients non spécialistes se trompent sur :

- l'ordre recto/verso et la somme constante des paires (n + 1) ;
- le nombre de pages, qui doit être un multiple de 4 ;
- les pages blanches (intérieur de couverture) et la couverture séparée ;
- le format du PDF livré : une page par page, ou deux pages côte à côte (vis-à-vis).

Cas réel à l'origine du projet : un cahier A5 de 16 pages (association APECD, Bénin), dont l'ordre et les pages blanches ont demandé de longues vérifications à la main.

Deuxième cas réel : le fichier était déjà mis en ordre d'impression, mais les pages n'étaient pas numérotées, ce qui rend toute vérification visuelle difficile et les erreurs presque invisibles avant le tirage. Les mêmes problèmes touchent les dépliants, les flyers et tout document plié : fautes, informations manquantes, mauvais ordre, découverts après l'impression.

**La vision.** « Voir son cahier avant de l'imprimer. » Le client dépose son PDF, le site détecte le format, feuillette un cahier virtuel, signale les erreurs et produit un rapport que l'imprimeur peut lire en 30 secondes.

**Hors sujet pour la V1.** Le site n'édite pas les PDF, n'est pas un logiciel de mise en page et ne remplace pas un contrôle professionnel de fichier (couleurs, encre).

## 2. Cibles

Deux cibles primaires, qui se rendent service mutuellement : les clients qui envoient un fichier, et les imprimeurs qui le reçoivent.

| Cible | Qui | Besoin principal | Priorité |
| --- | --- | --- | --- |
| Client non expert | Associations, ONG, écoles, églises, petites entreprises, auteurs | Savoir si son PDF est prêt à imprimer, sans connaître l'imposition | Primaire |
| Imprimeur / reprographe | Petites imprimeries, centres de photocopie, imprimeries d'école | Recevoir des fichiers corrects, éviter les reprises et les disputes | Primaire |
| Graphiste freelance | Designers qui livrent des brochures (Canva, Word, InDesign) | Un rapport à joindre à la livraison | Secondaire |
| Éditeur / institution | Maisons d'édition locales, universités (mémoires, annales), administrations | Volume régulier, cahiers de 32 à 64 pages | Secondaire, V3 |

**Personas de départ**

- *Une responsable d'association* : fait sa brochure sur Canva, ne connaît pas l'imposition, veut une réponse claire « prêt » ou « à corriger ».
- *Un imprimeur de quartier* : reçoit des PDF par WhatsApp, n'a pas de logiciel d'imposition, perd du temps à corriger l'ordre des pages.

**Zone géographique.** V1 : Bénin. Ensuite l'Afrique francophone (Togo, Côte d'Ivoire, Sénégal, Burkina Faso), puis l'anglophone (Nigeria, Ghana, Kenya).

**À valider avant de coder.** Entretiens avec 5 imprimeurs : quels fichiers reçoivent-ils, quelles erreurs reviennent le plus, utilisent-ils WhatsApp, quel prix accepteraient-ils de payer ?

## 3. Existant : monde, Afrique, prix

Des outils d'imposition, de flipbook et de contrôle de fichier existent, mais aucun trouvé ne combine vérification d'imposition, aperçu et rapport pour l'imprimeur, en français et payable en Mobile Money. Prix relevés dans des résultats de recherche, à re-vérifier avant toute décision.

| Outil | Type | Prix relevé | Limite pour notre cible |
| --- | --- | --- | --- |
| [PDF Press](https://alternativeto.net/software/pdf-press/about/) | Imposition + préflight, dans le navigateur | Gratuit limité, Pro 8 à 12 $/mois | Outil d'opérateur, pas de vérification pour client non expert |
| [PressCal](https://alternativeto.net/software/presscal/about/) | Devis + imposition pour imprimeries | 42 à 55 $/mois | Cher et en anglais, grec, espagnol, italien |
| [Impose Online](https://print21.com.au/news/free-pdf-imposition-tool-makes-online-debut) | Imposition en ligne (Australie) | Annoncé 1 $ par feuille imposée (ancien) | Pas d'aperçu pédagogique ; statut actuel à vérifier |
| [Cloudinary PDF Booklet Imposer](https://cloudinary.com/tools/pdf-booklet-imposer) | Imposition de livret, gratuit | Gratuit | Produit un PDF, ne dit pas si le fichier d'origine est bon |
| [Heyzine](https://techjockey.com/detail/heyzine) | Flipbook 3D de lecture | À partir de 14 $ (revendeur), 5 flipbooks gratuits | Sert à lire, pas à vérifier une impression |
| [Publuu](https://www.g2.com/compare/flipbookpdf-net-vs-heyzine-vs-publuu) | Flipbook | À partir de 7 $/mois | Idem |
| [Enfocus PitStop](https://printweek.com/content/product-news/enfocus-shows-off-pitstop-v13-at-fespa) | Contrôle de fichier professionnel (plug-in Acrobat) | 699 € la licence, ou 300 €/an (version 13, ancienne) | Hors de portée d'un petit imprimeur africain |
| [Callas pdfToolbox](https://www.rpimaging.com/products/callas-pdftoolbox-desktop) | Contrôle de fichier professionnel | Sur devis | Idem |

### Outils libres et gratuits d'imposition

Ces outils réorganisent les pages d'un PDF en cahier, mais aucune des sources consultées ne mentionne de contrôle de la numérotation, des fautes ou des informations manquantes, ni de validation en ligne à plusieurs. Printwarden est le plus proche : il offre déjà une 3D, un préflight et un visualiseur à envoyer au client, mais c'est une application de bureau en anglais.

| Outil | Ce qu'il fait | Ce qu'il ne fait pas (selon les sources) |
| --- | --- | --- |
| [Imposify](https://github.com/brad-arnett/imposify) (licence MIT, navigateur) | Réordonne un PDF en cahier, aperçu, ajoute des pages blanches pour atteindre un multiple de 4, détecte pages simples ou vis-à-vis, gère le sens droite-gauche | Deux pages par feuille seulement, un seul cahier ; fond perdu, décalage de pli, repères et épreuves sont encore à faire (feuille de route du projet) |
| [pdfjam, pdfcpu](https://pdfpress.app/fr/blog/open-source-imposition-software) (ligne de commande) | Imposition de livrets et de pages multiples, très bons pour l'automatisation | Réservés aux techniciens, pas d'interface pour un client |
| [Scribus](https://pdfpress.app/fr/blog/open-source-imposition-software) | Logiciel libre de mise en page, export PDF avec fond perdu et traits de coupe | L'imposition passe par des scripts ou par un autre outil |
| [PDF Arranger](https://pdfpress.app/fr/blog/open-source-imposition-software) | Réordonner, pivoter et rogner des pages visuellement | Pas de logique de cahier ni de rapport |
| [PDF Press](https://pdfpress.app/fr/blog/open-source-imposition-software) | Imposition dans le navigateur avec aperçu de la feuille, traitement local | Outil pour opérateur ; l'article qui le présente est publié par PDF Press lui-même |
| Printwarden | Visionneuse 3D du cahier (reliures, papier), préflight, export imposé avec décalage de pli, visualiseur HTML à envoyer au client ; licence Apache 2.0 (dépôt nyannurs/Printwarden sur GitHub) | **Application de bureau à installer (Windows, macOS), en anglais, pensée pour les graphistes sous InDesign ; ni validation en ligne ni contrôle de numérotation mentionnés ; le détail de son préflight n'est pas précisé** |
| PDF Mix Tool, PDF Imposer | Cités dans votre texte | **Non vérifiés** : je n'ai pas ouvert leurs pages ; PDF Mix Tool n'apparaît pas dans l'article de PDF Press que j'ai lu |

**Ce que cela change pour le projet**

1. *Ne pas réinventer l'imposition.* Imposify (licence MIT) utilise déjà les mêmes briques que celles prévues en section 7 (pdf.js et pdf-lib). L'export d'un PDF imposé, prévu en V2, devient une simple commodité, pas un argument de vente.
2. *La différence, c'est le contrôle et le partage.* Numérotation, pages blanches non déclarées, rapport en français pour l'imprimeur, validation par lien à plusieurs, puis fautes et informations manquantes en V3.
3. *Piste de gain de temps.* Reprendre ou s'inspirer du code d'Imposify pour la logique de cahier, après relecture de sa licence et accord de ses auteurs si nécessaire.
4. *Prochaine vérification.* Tester Printwarden et PDF Imposer à la main : si l'un d'eux contrôle déjà la numérotation, la différence de la V1 devra être précisée. La 3D de la V2 n'est plus un argument unique : Printwarden l'offre déjà.

**En Afrique.** Le seul acteur web-to-print documenté dans mes recherches est [Printivo](https://techcabal.com/2014/01/22/meet-printivo-web-enabled-demand-printing-platform/) (Nigeria, lancé en 2014) : commande en ligne de cartes de visite et de brochures à partir de modèles, avec [une levée de fonds d'amorçage](https://businesschief.eu/digital-strategy/nigerian-start-about-overhaul-africas-dollar6-billion-printing-sector). Il a lui-même intégré un éditeur tiers plutôt que de le développer ([étude de cas](https://customerscanvas.com/case-studies/printivo-case-study)). Le même article cite d'autres tentatives nigérianes qui sont revenues à la vente classique, ce qui rappelle que le web-to-print est difficile. Ailleurs, les imprimeries africaines ne ressortent que dans des annuaires, par exemple [Go Africa Online](https://www.goafricaonline.com/sn/609488-africa-print-impression-numerique-dakar-senegal), sans outil de vérification de fichier.

**Limite de cette recherche.** Je n'ai trouvé aucun outil africain dédié à la vérification d'imposition de cahier. Cela ne prouve pas qu'il n'en existe pas : une enquête de terrain auprès d'imprimeurs reste nécessaire.

**Ce qui manque sur le marché**

- une vérification expliquée en français pour non-spécialistes ;
- un rapport partageable avec l'imprimeur ;
- un prix adapté au pouvoir d'achat local, payable en Mobile Money ;
- un site léger, utilisable sur téléphone avec une connexion lente.

### Inspirations et réutilisation

On peut s'inspirer de Printwarden et d'Imposify et réutiliser une partie de leur code, à condition de respecter leurs licences ; on ne reprend ni leur nom, ni leur identité visuelle. Ce tableau n'est pas un avis juridique : faire relire par un juriste avant de publier.

| Élément | Source | Décision | Condition ou remarque |
| --- | --- | --- | --- |
| Organisation de l'écran (aperçu au centre, pages en bas, réglages sur le côté) | Printwarden | S'inspirer | Dessiner notre propre interface, simplifiée pour un client non expert |
| Deux vues : ordre de lecture et feuilles d'impression | Printwarden | S'inspirer (V1) | Notre vue « feuille par feuille » avec les paires recto/verso |
| Logique de cahier : réordonner les pages, ajouter des pages blanches, détecter pages simples ou vis-à-vis | Imposify (licence MIT) | Reprendre ou réécrire | Garder l'avis de droits d'auteur et le texte de la licence MIT |
| Aperçu 3D du cahier (V2) | Printwarden (Three.js, licence Apache 2.0) | Reprendre du code possible | Adapter une application de bureau (Electron) à une page web ; tester les performances sur téléphone |
| Visualiseur à envoyer au client | Printwarden | S'inspirer, puis ajouter la validation en ligne | C'est notre différence : lien, commentaires, bouton « Je valide » (V2) |
| Décalage de pli et plis de 8 ou 16 pages par feuille (V3) | Printwarden | Étudier sa documentation avant de coder | Lire le dossier `docs/` du dépôt pour les calculs d'impression |
| Nom, logo, identité visuelle | Printwarden | Ne pas reprendre | L'auteur indique avoir conçu lui-même son identité visuelle |
| Profil couleur ECI ISO Coated v2 | Printwarden | Ne pas reprendre | Exclu de la licence Apache 2.0 ; autorisation de l'ECI nécessaire |

**Obligations de la licence Apache 2.0** (résumé à faire confirmer)

- conserver le texte de la licence avec le code repris ;
- conserver les mentions de droits d'auteur et le fichier NOTICE ;
- indiquer les fichiers que nous avons modifiés ;
- ne pas utiliser le nom Printwarden pour promouvoir notre produit ;
- le logiciel est fourni sans garantie : nous restons responsables de la qualité de notre propre produit.

**Méthode**

1. Tester Printwarden et Imposify avec le PDF réel de l'APECD (16 pages) et noter ce qu'ils détectent ou ratent.
2. Lire `LICENSE`, `NOTICE` et `CREDITS.md` du dépôt Printwarden.
3. Décider, pour chaque brique, entre reprendre du code et le réécrire.
4. Tenir un registre de provenance : pour chaque morceau de code repris, sa source, sa licence et la date.
5. Prévoir sur le site une page « Crédits et licences ».

**Réserve sur la qualité.** L'auteur de Printwarden indique que le logiciel a été entièrement écrit avec un assistant de programmation. Je n'ai pas lu son code : sa qualité reste à tester avant de bâtir dessus.

## 4. Positionnement et proposition de valeur

Le produit se place entre le flipbook (on lit) et le logiciel d'imposition (on produit) : il dit si le fichier est bon, et pourquoi.

**Promesse.** « Déposez votre PDF, voyez votre cahier, sachez en 30 secondes s'il est prêt pour l'imprimeur. »

**Trois différenciateurs**

1. *Vérification expliquée* : chaque alerte dit ce qui ne va pas et comment corriger, en français simple.
2. *Rapport pour l'imprimeur* : un lien ou un PDF avec format, nombre de pages, paires recto/verso et pages blanches.
3. *Adapté au contexte local* : léger sur mobile, Mobile Money, prix en FCFA, français d'abord.

**Ce que le produit n'est pas**

- un éditeur de PDF ou un logiciel de mise en page ;
- un service d'impression (avant la V4) ;
- un contrôle de couleurs ou d'encre de niveau professionnel (avant la V3).

## 5. Fonctionnalités par version

La V1 se concentre sur le problème le plus urgent, l'ordre et la numérotation des pages d'un cahier agrafé, avec vérification et aperçu en 2D, sans compte ni paiement ; la 3D arrive en V2, car elle aide à comprendre mais ne résout pas le problème d'ordre.

### Vue d'ensemble des versions

| Version | Thème | Problème traité | Produits concernés |
| --- | --- | --- | --- |
| V1 | Cahiers agrafés : ordre des pages et numérotation | Le plus urgent : l'ordre, les pages blanches, les pages non numérotées | Brochures, livrets agrafés |
| V2 | Autres documents imprimés et validation partagée | Dépliants mal pliés, validation par plusieurs personnes avant tirage | Dépliants, flyers, cartes, affiches ; aperçu 3D |
| V3 | Contrôle intelligent et API | Fautes, informations manquantes, incohérences, qualité du fichier | Tous les documents ; imprimeries partenaires |
| V4 | Textiles et commande en ligne | Aperçu avant impression sur vêtements, commande et suivi | Polos, T-shirts, casquettes, autres supports |

### V1 : MVP « Vérifier et voir » (sans compte, gratuit)

- Dépôt d'un PDF (limite de départ : 50 Mo) traité dans le navigateur.
- Détection automatique : nombre de pages, taille en mm, pages simples ou deux pages côte à côte. En cas de doute, l'utilisateur confirme.
- Réglages en 3 questions : format du fichier, couverture seule ou non, intérieur de couverture blanc ou non.
- Contrôle du nombre de pages (multiple de 4) avec proposition d'ajouter des pages blanches.
- Aperçu 2D feuilletable du cahier, couverture seule puis pages en vis-à-vis.
- Vue « feuille par feuille » : ce qui sort au recto et au verso de chaque feuille (16 | 1, 2 | 15, etc.).
- Alertes claires en français, avec la correction proposée.
- Rapport de vérification : lien partageable et PDF d'une page à envoyer à l'imprimeur.
- Suppression automatique du fichier et du lien après 24 heures.
- Interface en français, utilisable sur téléphone.

**Ajouts V1 après les retours de l'imprimeur : numérotation et montage**

- Contrôle de la numérotation : pages sans numéro, numéros en double, manquants ou dans le mauvais ordre, par lecture du texte du PDF. Un PDF fait d'images (scan) ne peut pas être lu de cette façon avant la V3.
- Comparaison entre le numéro imprimé et la position de la page (la page 5 affiche-t-elle bien 5 ?), en acceptant une couverture non numérotée.
- Détection des pages blanches non déclarées.
- Choix du montage dès le départ : le rapport indique clairement s'il s'agit d'un cahier de 16 pages couverture incluse ou d'une couverture séparée (voir section 6), pour éviter les malentendus avec l'imprimeur.

### V2 : « Aperçu 3D et export »

- Comptes utilisateurs et historique des vérifications.
- Aperçu 3D du cahier fermé et ouvert, avec dos et épaisseur.
- Export d'un PDF déjà imposé, prêt pour l'imprimeur (agrafé, A4 paysage pour du A5).
- Couverture séparée sur papier épais (cahier intérieur + couverture de 4 pages).
- Contrôle de base du fond perdu (cadres du PDF) et de la taille des pages.
- Espace imprimeur : réception des fichiers, statut « vérifié », messages.
- Paiement Mobile Money et carte, offres gratuite et payante.
- Anglais en plus du français.

**Ajouts V2 : autres documents et validation partagée**

- Modèles de dépliants et flyers : 2 volets, 3 volets roulés, pli en Z, accordéon, flyer recto-verso, carte. Chacun avec son ordre de panneaux et ses règles de pliage.
- Lien de partage privé pour chaque création, consultable sans compte, avec date d'expiration.
- Commentaires sur les pages et bouton « Je valide » : un bon à tirer numérique avec nom, date et version du fichier, pour que tous les décideurs valident avant impression.
- Historique des versions : on voit ce qui a changé entre deux envois.

### V3 : « Contrôle avancé et API »

- Contrôle de fichier : polices intégrées, RVB ou CMJN, résolution des images, couverture d'encre.
- Décalage de pli (creep) pour les cahiers épais, plusieurs cahiers pour les gros volumes.
- Autres reliures : dos carré collé, spirale.
- API et bouton intégrable (« Vérifier mon fichier ») pour les sites d'imprimeurs, avec leur marque.
- Calcul du nombre de feuilles et estimation de coût pour l'imprimeur.
- Sens de lecture de droite à gauche (arabe).

**Ajouts V3 : contrôle par IA**

- Fautes d'orthographe et de grammaire, avec suggestions que l'utilisateur accepte ou refuse une à une. L'IA propose, elle ne corrige jamais seule : elle peut se tromper, surtout sur les noms propres et les termes locaux.
- Informations manquantes ou incohérentes : téléphone, courriel, adresse, date, mentions obligatoires, un numéro de téléphone différent d'une page à l'autre.
- Liste de contrôle personnalisable selon le type de document.
- Lecture des PDF scannés (OCR) pour la numérotation.
- Consentement explicite avant d'envoyer le texte à un service d'IA, car le contenu des clients est confidentiel.

### V4 : « Plateforme »

- Commande d'impression en ligne chez des imprimeurs partenaires, suivi et livraison.
- Application web installable (PWA) avec usage hors ligne.
- Détection d'erreurs d'ordre par lecture des numéros de page (OCR).
- Modèles prêts à imprimer pour Canva et Word.
- Autres langues, dont des langues locales.

**Ajouts V4 : textiles (polos, T-shirts, casquettes)**

- Aperçu du visuel sur le vêtement avant impression, avec validation par lien comme en V2.
- Zones d'impression par vêtement, nombre de couleurs, taille minimale des textes, tailles des vêtements.
- Contrôle de la qualité du logo (résolution, format vectoriel).
- Commande et suivi avec les imprimeurs textiles partenaires.
- C'est un autre métier que la reliure : autres clients, autres contraintes, d'où sa place à part, après stabilisation des versions V1 à V3.

### Hors périmètre, toutes versions

- Édition du contenu des PDF.
- Conception graphique des pages.

## 6. Règles métier d'imposition de cahier

Pour un cahier agrafé de n pages (n multiple de 4), il y a n / 4 feuilles ; sur chaque face, la somme des deux numéros de page vaut n + 1.

**Formule pour la feuille k** (k de 1 à n / 4) :

- recto : page (n − 2k + 2) à gauche, page (2k − 1) à droite ;
- verso : page (2k) à gauche, page (n − 2k + 1) à droite.

**Exemple, n = 16** (cas A choisi : pages 2 et 15 blanches)

| Feuille | Recto (gauche \| droite) | Verso (gauche \| droite) |
| --- | --- | --- |
| 1 | 16 \| 1 | 2 \| 15 |
| 2 | 14 \| 3 | 4 \| 13 |
| 3 | 12 \| 5 | 6 \| 11 |
| 4 | 10 \| 7 | 8 \| 9 |

**Règles à implémenter**

1. *Contrôle de somme* : toute paire dont la somme n'est pas n + 1 est une erreur. C'est le test qui aurait détecté le schéma manuscrit où 15 | 4 donnait 19.
2. *Multiple de 4* : sinon, proposer d'ajouter 1 à 3 pages blanches avant le dos de couverture.
3. *Couverture* : page 1 = couverture, dernière page = dos. Intérieur de couverture blanc = pages 2 et n − 1 vides, donc seulement n − 4 pages de contenu.
4. *Détection du format* : un PDF dont les pages sont nettement plus larges que hautes (ratio proche de 1,41 pour deux pages A5 côte à côte) est probablement en vis-à-vis. Toujours demander confirmation, car un A4 paysage seul a le même ratio.
5. *Couverture séparée* : si elle est imprimée sur papier épais, calculer le cahier intérieur et la couverture séparément.
6. *Fond perdu* : 3 mm par défaut ; lecture des cadres du PDF (MediaBox, TrimBox, BleedBox) en V2.
7. *Cahiers épais* : au-delà d'un certain nombre de feuilles pliées, le papier décale les marges ; le seuil dépend du papier et est à valider avec des imprimeurs (V3).

**Trois montages à distinguer** (source de la confusion entre client et imprimeur)

| Montage | Description | Pages de contenu | Pages blanches |
| --- | --- | --- | --- |
| A | 16 pages, couverture incluse, intérieur de couverture blanc | 12 (pages 3 à 14) | Pages 2 et 15 |
| B | Couverture séparée sur papier épais + 14 pages intérieures | 14 | 2 pages en fin de cahier, couverture à part |
| C | 16 pages, couverture incluse, pages 2 et 15 imprimées | 14 (pages 2 à 15) | Aucune |

Le montage C est celui validé pour le cahier de l'association. Le rapport de vérification doit toujours nommer le montage choisi, car une même série de 16 pages peut être comprise de trois façons différentes.

## 7. Choix techniques

Recommandation : Next.js (React) avec TypeScript, pdf.js et pdf-lib dans le navigateur pour la V1, puis Three.js pour la 3D en V2 et un petit serveur Python pour l'analyse avancée en V3.

**Réponse à votre question.** Ces technologies ne sont pas concurrentes mais complémentaires : React est la bibliothèque d'interface, Next.js est le framework qui l'organise (pages, serveur, déploiement), Three.js est la bibliothèque 3D (en JavaScript), et Python sert surtout au traitement lourd de PDF côté serveur. Python seul ne peut pas afficher une 3D fluide dans le navigateur.

| Brique | Choix recommandé | Pourquoi | Alternative |
| --- | --- | --- | --- |
| Interface | Next.js + React + TypeScript + Tailwind | Un seul langage, déploiement simple, bon écosystème | Vue/Nuxt, SvelteKit |
| Lecture du PDF | pdf.js | Rendu des pages en image, dans le navigateur | PDFium (WebAssembly) |
| Réorganisation des pages | pdf-lib (JavaScript) | Génère le PDF imposé sans serveur | pikepdf côté Python |
| 3D (V2) | Three.js via React Three Fiber | Tourne de page réaliste, standard du marché | Babylon.js |
| Analyse avancée (V3) | Python + FastAPI, avec PyMuPDF, pikepdf, Ghostscript | Cadres du PDF, polices, images, encre | Node + qpdf |
| Base de données | PostgreSQL (ex. Supabase ou Neon) | Comptes, historique, rapports | SQLite au départ |
| Stockage | Compatible S3 (Cloudflare R2, Backblaze B2) | Coût bas, suppression automatique | Disque du serveur |
| Paiement | KKiaPay ou FedaPay | Mobile Money MTN et Moov, cartes | pawaPay, Stripe pour la diaspora |
| Hébergement | Vercel ou Cloudflare (interface), petit VPS (traitement) | Gratuit pour démarrer | Serveur local |

**Pourquoi tout faire dans le navigateur en V1.** Le fichier reste sur l'appareil de l'utilisateur : pas de coût de serveur, pas de risque de confidentialité, et résultat immédiat. [PDF Press](https://alternativeto.net/software/pdf-press/about/) fonctionne ainsi.

**Point de vigilance sur les licences.** Certaines bibliothèques de PDF (Ghostscript, PyMuPDF) sont sous licence AGPL, ce qui impose de publier le code ou d'acheter une licence commerciale pour un service en ligne. À faire vérifier avant la V3 ; pdf-lib et pdf.js sont sous licences permissives (à confirmer).

## 8. Architecture, sécurité, confidentialité

En V1 tout se passe dans le navigateur ; le serveur n'apparaît qu'à partir de la V2, pour les comptes, le stockage temporaire et le paiement.

**Exigences**

- *Confidentialité* : les fichiers des clients sont privés. V1 : aucun envoi de fichier au serveur. V2 : stockage chiffré, lien de partage aléatoire non devinable, suppression automatique après 24 heures par défaut (durée modifiable).
- *Performance* : premier aperçu en moins de 10 secondes pour un PDF de 16 pages sur connexion mobile ; images réduites pour l'aperçu.
- *Disponibilité* : site léger (PWA en V4) pour les connexions lentes.
- *Données personnelles* : politique de confidentialité claire, avec avis d'un juriste sur la loi béninoise de protection des données et sur le RGPD pour les clients européens.
- *Sécurité* : analyse des PDF dans un environnement isolé en V3 (un PDF peut être piégé), limite de taille et de nombre de pages, protection contre l'abus (limites de requêtes).

&#91;embedded content: architecture · 3 niveaux V1, V2, V3\]

En V1 le fichier ne quitte pas l'appareil ; le serveur sert aux comptes, au stockage temporaire et au paiement dès la V2, puis à l'analyse avancée en V3.

## 9. Modèle économique et paiement

La V1 est gratuite pour gagner la confiance des imprimeurs ; les revenus viennent ensuite des imprimeurs (abonnement) et des exports à l'unité pour les clients.

**Hypothèses de départ, à tester par entretiens** (prix en FCFA indicatifs, non issus d'une étude de marché) :

| Offre | Pour qui | Contenu | Prix de départ à tester |
| --- | --- | --- | --- |
| Gratuite | Clients | Vérification, aperçu 2D, rapport (avec mention du site) | 0 |
| À l'unité | Clients | Export du PDF imposé, rapport sans mention | Environ 500 à 1 000 FCFA par fichier |
| Imprimeur | Petites imprimeries | Espace de réception, fichiers illimités, rapport à son nom | Environ 5 000 à 15 000 FCFA par mois |
| API / marque blanche | Grosses imprimeries, éditeurs | Bouton intégrable, volume | Sur devis (V3) |

**Repères de prix des concurrents** : PDF Press 8 à 12 $ par mois, Heyzine à partir de 14 $, PressCal 42 à 55 $ par mois (sources en section 3). Un outil africain doit se placer nettement en dessous pour les petites imprimeries.

**Paiement.** [KKiaPay](https://docs.kkiapay.me/v1/paiements/methodes-de-paiement) et [FedaPay](https://docs.fedapay.com/payments/payment-methods) acceptent MTN Mobile Money, Moov Money et les cartes Visa et Mastercard au Bénin. Un [comparatif de 2026](https://kolonell.com/fr/blog/kkiapay-fedapay-benin-integration-marchand-cotonou-2026) estime les frais en Mobile Money à environ 1,5 à 2,5 %, avec versement sous 1 à 2 jours ; un ancien article de presse parlait de [3 %](https://cio-mag.com/au-benin-kkiapay-se-positionne-comme-le-premier-agregateur-de-paiement/). À confirmer auprès de chaque fournisseur. KKiaPay se branche plus vite (widget), FedaPay offre une API plus complète pour les abonnements récurrents, selon ce comparatif.

**Autres revenus possibles** : commission sur les commandes d'impression (V4), publicité de fournisseurs de papier, formations à l'imposition pour imprimeurs.

## 10. Risques, indicateurs, planning, sources

Le risque principal n'est pas technique mais commercial : convaincre les imprimeurs de l'utiliser. Il se traite en testant avec deux ou trois imprimeurs réels avant la V2.

**Risques**

| Risque | Impact | Parade |
| --- | --- | --- |
| Les imprimeurs n'adoptent pas l'outil | Élevé | Entretiens avant de coder, test avec 2 à 3 imprimeurs, bouton à leur marque |
| Détection de format erronée (A4 paysage ou A5 en vis-à-vis) | Moyen | Toujours demander confirmation, aperçu visuel |
| Promesse de contrôle d'encre ou de couleur non tenue | Élevé | Ne pas promettre le CMJN avant la V3 ; mentionner les limites dans le rapport |
| Fichiers volumineux et connexions lentes | Moyen | Traitement local, images réduites, limite de taille |
| Concurrence gratuite (PDF Press, Cloudinary) | Moyen | Se différencier par l'explication en français, le rapport et le Mobile Money |
| Coût d'hébergement en V2 | Faible à moyen | Suppression automatique, limites par utilisateur |
| Licences AGPL (Ghostscript, PyMuPDF) | Moyen | Avis juridique ou bibliothèques alternatives avant la V3 |
| Confusion entre montages (16 pages couverture incluse, couverture séparée, pages blanches) | Élevé | Choisir le montage dès le départ ; le rapport nomme toujours le montage et la liste des pages blanches |
| Numérotation illisible (PDF scanné, numéros dans des images) | Moyen | Dire que la lecture est impossible et demander une confirmation manuelle ; lecture par OCR en V3 |
| Trop de produits trop tôt (dépliants, textiles) qui diluent la V1 | Élevé | V1 limitée aux cahiers ; passage à la version suivante seulement si la condition du planning est remplie ; textiles réservés à la V4 |
| Erreurs de l'IA (fausses fautes, noms propres et termes locaux mal compris) en V3 | Moyen | L'IA propose, l'utilisateur décide ; jamais de correction automatique ; mention claire des limites |
| Confidentialité du texte envoyé à un service d'IA | Moyen à élevé | Consentement explicite, option de désactiver l'IA, avis juridique avant la V3 |
| Validation par lien contestée (qui a validé quoi, et quelle version) | Moyen | Chaque validation garde le nom, la date et l'empreinte de la version du fichier (V2) |

**Indicateurs de succès de la V1** (cibles de départ à ajuster)

- 100 PDF vérifiés dans le premier mois ;
- plus de 30 % des rapports partagés avec un imprimeur ;
- au moins 3 imprimeurs qui l'utilisent chaque semaine ;
- première vérification terminée en moins de 60 secondes ;
- au moins 5 retours d'utilisateurs recueillis.

**Planning estimé** (1 développeur, à ajuster)

| Version | Durée estimée | Condition pour passer à la suivante |
| --- | --- | --- |
| Entretiens et maquettes | 2 semaines | 5 imprimeurs interrogés |
| V1 | 5 à 7 semaines (numérotation et montages inclus) | Utilisée par 3 imprimeurs |
| V2 | 8 à 10 semaines (dépliants et validation par lien inclus) | Premiers paiements et premières validations par lien |
| V3 | 10 à 12 semaines (contrôle par IA inclus) | Imprimeurs abonnés |
| V4 | 6 mois ou plus (textiles et commande en ligne, étude séparée avant lancement) | Volume de commandes suffisant |

**Prochaines étapes**

- [ ] Choisir un nom et un nom de domaine.
- [ ] Interroger 5 imprimeurs (Bénin d'abord).
- [ ] Tester avec 3 vrais PDF, dont le cahier de 16 pages de l'APECD.
- [ ] Valider la liste des fonctionnalités de la V1.
- [ ] Construire le prototype V1.

**Sources** (pages consultées via recherche web, octobre 2026 ; prix à re-vérifier)

- [PDF Press](https://alternativeto.net/software/pdf-press/about/) · [PressCal](https://alternativeto.net/software/presscal/about/)
- [Impose Online](https://print21.com.au/news/free-pdf-imposition-tool-makes-online-debut) · [Cloudinary PDF Booklet Imposer](https://cloudinary.com/tools/pdf-booklet-imposer)
- [Heyzine (Techjockey)](https://techjockey.com/detail/heyzine) · [Heyzine, Publuu (G2)](https://www.g2.com/compare/flipbookpdf-net-vs-heyzine-vs-publuu)
- [Enfocus PitStop 13 (PrintWeek)](https://printweek.com/content/product-news/enfocus-shows-off-pitstop-v13-at-fespa) · [Callas pdfToolbox](https://www.rpimaging.com/products/callas-pdftoolbox-desktop)
- [Printivo (TechCabal)](https://techcabal.com/2014/01/22/meet-printivo-web-enabled-demand-printing-platform/) · [Printivo (Business Chief)](https://businesschief.eu/digital-strategy/nigerian-start-about-overhaul-africas-dollar6-billion-printing-sector) · [Printivo (Customer's Canvas)](https://customerscanvas.com/case-studies/printivo-case-study)
- [KKiaPay](https://docs.kkiapay.me/v1/paiements/methodes-de-paiement) · [FedaPay](https://docs.fedapay.com/payments/payment-methods) · [Comparatif Kolonell](https://kolonell.com/fr/blog/kkiapay-fedapay-benin-integration-marchand-cotonou-2026) · [CIO Mag](https://cio-mag.com/au-benin-kkiapay-se-positionne-comme-le-premier-agregateur-de-paiement/)
