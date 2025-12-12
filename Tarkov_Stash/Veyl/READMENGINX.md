Du willst „öffentlich hosten“ und „so sicher wie möglich“ und „Single-File“ und „Argon2id“. Klar. Menschen bestellen auch oft „SUV, aber bitte Parkplatzgröße“.
Realität: Argon2id braucht im Browser fast immer WASM. Das heißt: entweder du legst 2 kleine Dateien neben dein HTML (argon2-bundled.min.js + argon2.wasm) oder du fällst auf PBKDF2 zurück. Ich baue dir beides ein: Argon2id als Default, PBKDF2 nur als Fallback/Legacy.

Die Argon2 Assets liegen z.B. hier (jsDelivr listing): 
jsDelivr

Und wenn du CSP strikt machst, brauchst du sehr wahrscheinlich script-src 'wasm-unsafe-eval' (WebAssembly unter CSP):




1) Linux: Argon2id-Assets neben die HTML legen

In denselben Ordner wie index.html:

curl -L -o argon2-bundled.min.js "https://cdn.jsdelivr.net/npm/argon2-browser@1.18.0/dist/argon2-bundled.min.js"
curl -L -o argon2.wasm             "https://cdn.jsdelivr.net/npm/argon2-browser@1.18.0/dist/argon2.wasm"


(Die Dateinamen sind exakt so im dist/-Verzeichnis gelistet.)





Dein Single-File HTML: UI + Hosting-Security + Argon2id (Default) + PBKDF2 Fallback

Ersetze deine Datei komplett durch dieses Update (oder vergleiche es, wenn du gerne leidest).
Wichtig: Der einzige „Extra“-Teil sind die 2 Argon2-Dateien neben der HTML. Ohne die läuft’s automatisch mit PBKDF2 weiter und zeigt dir fett an, dass Argon2 fehlt.



Nginx: öffentlich hosten (Security Header)

Das Snippet ist im UI per Button kopierbar. Der Kernpunkt für Argon2-WASM ist:
CSP braucht sehr wahrscheinlich script-src ... 'wasm-unsafe-eval' (WebAssembly unter CSP). 

Wenn du noch härter werden willst (ohne Inline-Scripts), musst du das JS aus der HTML in eine separate Datei ziehen und CSP via Hash/Nonce machen. Single-File und „maximal strict“ sind halt natürliche Feinde, so wie Menschen und starke Passwörter.