# Prague Boats Media Hub - PHP

PHP verze media hubu Prague Boats s ochranou heslem.

## Struktura projektu

```
prague-boats-media/
├── config/
│   └── auth.php              # Konfigurace hesla (JEDINÝ soubor pro změnu hesla)
├── includes/
│   ├── auth_guard.php         # Ochrana stránek - přesměruje nepřihlášené na login
│   ├── header.php             # HTML hlavička + sidebar navigace + logout tlačítko
│   └── footer.php             # Patička + JavaScript
├── public/                    # WEBROOT - nastavit jako document root
│   ├── .htaccess              # Apache rewrite pravidla
│   ├── index.php              # Hlavní stránka (chráněná heslem)
│   ├── login.php              # Přihlašovací stránka (veřejná)
│   ├── logout.php             # Odhlášení
│   ├── styles.css             # CSS styly
│   ├── logo/
│   │   └── logo.svg           # Logo Prague Boats
│   ├── thumbs/                # Náhledy obrázků (nutno doplnit z originálu)
│   └── media/
│       └── content/           # Video soubory (nutno doplnit z originálu)
└── README.md
```

## Nasazení

### Apache

1. Nahrajte celý projekt na server.
2. Nastavte **document root** na složku `public/`.
3. Ověřte, že je povolený `mod_rewrite` a `.htaccess`:
   ```apache
   <Directory /var/www/html/public>
       AllowOverride All
       Require all granted
   </Directory>
   ```
4. Restartujte Apache: `sudo systemctl restart apache2`

### Nginx

1. Nahrajte celý projekt na server.
2. Nastavte konfiguraci:
   ```nginx
   server {
       listen 80;
       server_name media.prague-boats.cz;
       root /var/www/prague-boats-media/public;
       index index.php;

       location / {
           try_files $uri $uri/ /index.php?$query_string;
       }

       location ~ \.php$ {
           fastcgi_pass unix:/var/run/php/php-fpm.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.(ht|git) {
           deny all;
       }
   }
   ```
3. Restartujte Nginx: `sudo systemctl restart nginx`

### Sdílený hosting

1. Nahrajte obsah složky `public/` do hlavního adresáře webu (public_html / www).
2. Složky `config/` a `includes/` nahrajte **o úroveň výš** (mimo public_html).
3. Upravte cesty v souborech:
   - `index.php`: změňte `__DIR__ . '/../includes/...'` na skutečnou cestu
   - `login.php`: obdobně
   - `auth_guard.php`: obdobně

## Změna hesla

1. Otevřete soubor `config/auth.php`.
2. Vygenerujte nový hash hesla:
   ```bash
   php -r "echo password_hash('vase_nove_heslo', PASSWORD_DEFAULT);"
   ```
3. Nahraďte hash v `AUTH_PASSWORD_HASH` vygenerovaným hashem.
4. Hotovo. Žádný jiný soubor měnit nemusíte.

**Výchozí heslo:** `prague2025`

## Přihlášení

- Po vstupu na web se zobrazí přihlašovací formulář.
- Po zadání správného hesla se uživatel dostane na požadovanou stránku.
- Odhlášení je možné přes odkaz "Odhlásit se" v dolní části sidebaru.
- Session je zabezpečena přes `session_regenerate_id()` po přihlášení.

## Chybějící assety

Po nasazení je potřeba doplnit:

- **Náhledy** (`public/thumbs/`) - zkopírujte z originálu nebo stáhněte z `https://media.prague-boats.cz/thumbs/`
- **Videa** (`public/media/content/`) - zkopírujte z originálu
