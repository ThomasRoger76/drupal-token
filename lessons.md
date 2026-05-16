# Leçons — drupal-token

Problèmes avec les tokens Drupal découverts en projet réel.

---

### 2026-05-16 — Token `[node:field_image:entity:file:url]` retourne vide avec Media

- **Symptôme :** L'URL de l'image dans Metatag est vide bien que le nœud ait une image
- **Cause :** Le champ Image est de type "Entity Reference → Media" (pas "File" direct). Le chemin du token est différent.
- **Correct :** `[node:field_image:entity:field_media_image:entity:url]` (passe par le Media entity puis le File)
- **Prévention :** Tester les tokens dans "Browse available tokens" avant de les utiliser en production

### 2026-05-16 — Token non résolu visible dans le texte — `[node:field_optionnel]` affiché

- **Symptôme :** La meta description affiche littéralement `[node:field_resume]` pour les articles sans résumé
- **Cause :** `['clear' => FALSE]` (défaut) laisse les tokens non résolus dans le texte
- **Correct :** `token->replace($text, $data, ['clear' => TRUE])` — supprime les tokens vides
- **Prévention :** Toujours utiliser `['clear' => TRUE]` OU définir un fallback dans le token chain `[node:field_resume|node:body:summary]`

### 2026-05-16 — `hook_tokens()` pas appelé — type incorrect

- **Symptôme :** Le token custom `[mon_type:ma_valeur]` n'est jamais remplacé
- **Cause :** Le type déclaré dans `hook_token_info()` (`mon_module_commande`) ne correspond pas au type passé dans le contexte (`$data['commande']`)
- **Correct :** Le type dans `hook_token_info()` doit matcher la clé de `$data` dans `hook_tokens()`
- **Prévention :** Vérifier les types avec `drush php:eval "var_dump(array_keys(\Drupal::service('token')->getInfo()['tokens']));"` 

### 2026-05-16 — `token_replace()` fonction D7 — erreur PHP en D10+

- **Symptôme :** `Call to undefined function token_replace()` après upgrade
- **Cause :** Ancienne fonction D7 supprimée en D10
- **Correct :** `\Drupal::service('token')->replace($text, $data, ['clear' => TRUE])`
- **Prévention :** Chercher `token_replace(` avant tout upgrade D9→D10
