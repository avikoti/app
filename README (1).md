# Carnet de tontine

Application de gestion des tontines (Tontine et Adogbè), branchée sur Supabase.
Le site est un seul fichier, `index.html`, sans étape de compilation.

## Mise en ligne sur GitHub Pages

1. Sur GitHub, créez un dépôt (par exemple `carnet-tontine`).
2. Envoyez `index.html` et `README.md` à la racine du dépôt (bouton « Add file », puis « Upload files »).
3. Dans le dépôt : Settings, puis Pages. Choisissez « Deploy from a branch », la branche `main` et le dossier `/ (root)`.
4. Après une minute, le site est disponible à l'adresse `https://VOTRE-NOM.github.io/carnet-tontine/`.

## Avant la première connexion (Supabase)

1. **Créer le compte administrateur** : Authentication, puis Users, puis « Add user ». Saisissez votre e-mail et un mot de passe, et cochez « Auto Confirm User ».
2. **Lui donner le rôle administrateur** : exécutez dans le SQL Editor (en remplaçant l'e-mail) :
   ```sql
   update auth.users
   set raw_app_meta_data = coalesce(raw_app_meta_data, '{}'::jsonb) || '{"role":"admin"}'::jsonb
   where email = 'votre@email.com';
   ```
3. **Fermer les inscriptions** : Authentication, puis Sign In / Providers, puis Email. Désactivez « Allow new users to sign up ».

Sans le rôle `admin`, la connexion affiche « Accès réservé » et aucune donnée n'est lisible.

## Lien de suivi des membres

Le lien à envoyer aux membres est l'adresse du site suivie de `#suivi`, par exemple `https://VOTRE-NOM.github.io/carnet-tontine/#suivi`. Le membre saisit son identifiant (`TN-123456`) et voit ses propres tontines, en lecture seule. Les essais sont limités à 10 par quart d'heure et par adresse.

## Ajouter les cartes d'Adogbè

Dans le SQL Editor, par exemple :

```sql
insert into public.cards (id, type_id, value, name)
values ('a500', 'adogbe', 500, 'Carte 500 Adogbè'),
       ('a1000', 'adogbe', 1000, 'Carte 1 000 Adogbè');
```

Le site les affiche aussitôt dans le choix des cartes des membres Adogbè.

## Sécurité

- La clé qui figure dans `index.html` est une clé publique (« publishable ») : elle peut être visible. La protection vient des règles d'accès (RLS) de la base : seul un compte avec le rôle `admin` lit ou écrit les données des membres.
- Le seul accès public est la fonction `member_tracking`, qui ne renvoie que les données du membre dont on saisit l'identifiant.
