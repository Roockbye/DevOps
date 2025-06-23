
# Mise en place d’une pipeline de CI/CD

### 1. Si vous n’en possédez pas déjà un, créez vous un compte Github et lié votre compte via clé SSH à votre ordinateur

https://docs.github.com/fr/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

https://docs.github.com/fr/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
```
$ eval "$(ssh-agent -s)"
> Agent pid 59566
```
```
ssh-add ~/.ssh/id_ed25519
```
```
cat ~/.ssh/id_ed25519.pub
# Then select and copy the contents of the id_ed25519.pub file
# displayed in the terminal to your clipboard
```

- Dans le coin supérieur droit de n’importe quelle page sur GitHub, cliquez sur votre photo de profil, puis sur Paramètres

- Dans la section « Accès » de la barre latérale, cliquez sur Clés SSH et GPG.

- Cliquez sur Nouvelle clé SSH ou Ajouter une clé SSH.

- Dans le champ « Titre », ajoutez une étiquette descriptive pour la nouvelle clé. Par exemple, si vous utilisez un ordinateur portable personnel, vous pouvez nommer cette clé « Ordinateur portable personnel »

- Sélectionnez le type de clé : authentification ou signature. Pour plus d’informations sur la signature de commit, consultez À propos de la vérification des signatures de commit.

- Dans le champ « Clé », collez votre clé publique.

- Cliquez sur Ajouter une clé SSH.

- Si vous y êtes invité, confirmez l’accès à votre compte dans GitHub

```
rocky@pacman:~/Documents/DevOps/TP_CI$ ssh -T git@github.com
Hi Roockbye! You've successfully authenticated, but GitHub does not provide shell access.
```

[ssh](../captures/ssh.png)

## 2. Tester un premier workflow Github avec l’exemple suivant : https://docs.github.com/fr/actions/quickstart


