# crispy-octo-guacamole

Or how to release-deploy to a local repo...


Een voorbeeld om naar een lokale directory te releasen ipv. een online repository. Gebruik de reguliere maven release commando's:

```
mvn release:prepare
mvn release:perform
```

Om van een bepaalde tag de release artifact (nog eens) te maken:

```
git checkout <tag naam>
mvn deploy
```

zie verder de pom file.
