# Raketa

Web projektu Raketa semináře IVT Gymnázia Židlochovice. Stránka je statická a ke svému provozu nepotřebuje instalovat závislosti ani sestavovat aplikaci.

## Spuštění na vlastním počítači

Otevřete `index.html` v prohlížeči. Pro lokální HTTP server můžete v kořeni projektu použít také Python:

```shell
python -m http.server 8000 
```

Poté otevřete <http://localhost:8000>. Server ukončíte pomocí `Ctrl+C`.

- `index.html` obsahuje strukturu a texty stránky.
- `styles.css` obsahuje vzhled stránky.
- `script.js` zajišťuje interakci navigace.

## Automatické nasazení

Workflow [deploy.yml](.github/workflows/deploy.yml) se spouští při každém pushi do větve `main`. Připojí se přes SSH k serveru a v adresáři `/var/www/seminar.waldhans.cz/web` načte větev `main` z remote `origin` a nastaví podle ní pracovní kopii.

### Příprava serveru

1. Připravte webový server tak, aby poskytoval soubory z `/var/www/seminar.waldhans.cz/web`.
2. Na serveru nainstalujte Git a naklonujte tento repozitář do uvedeného adresáře. Cílový adresář musí být prázdný nebo nesmí existovat:

   ```shell
   git clone https://github.com/mwaldhans/seminar.waldhans.cz.git /var/www/seminar.waldhans.cz/web
   ```

3. Uživatel pro nasazování musí mít k adresáři a souborům repozitáře potřebná práva a musí se umět na server připojit pomocí SSH klíče. Server také musí mít přístup k repozitáři na GitHubu pro `git fetch` (u soukromého repozitáře včetně autentizace).
4. Pokud používáte jinou cestu na serveru, upravte příkaz `cd` ve workflow.

### GitHub Secrets

V repozitáři otevřete **Settings → Secrets and variables → Actions** a přidejte následující repository secrets:

| Secret | Hodnota |
| --- | --- |
| `SSH_HOST` | Adresa serveru |
| `SSH_USER` | Uživatelské jméno pro připojení |
| `SSH_PRIVATE_KEY` | Soukromý SSH klíč odpovídající veřejnému klíči povolenému na serveru |
| `SSH_PORT` | Port SSH; nepovinný, výchozí je `22` |

Soukromý klíč nevkládejte do repozitáře. Přístup pro `git fetch` ze serveru na GitHub se nastavuje samostatně.

### Průběh a kontrola

Po pushi do `main` sledujte výsledek v záložce **Actions** v GitHubu. Workflow zastaví skript při chybě; souběžné deploye pro stejnou větev neběží současně. GitHub však může nahradit čekající běh novějším, takže není zaručeno nasazení každého jednotlivého pushe.

**Pozor:** `git reset --hard origin/main` zahodí na serveru lokální změny sledovaných souborů. Nesledované soubory nemaže. Upravujte proto projekt v repozitáři, nikoli přímo na serveru.
