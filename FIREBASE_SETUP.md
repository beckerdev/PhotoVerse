# FIREBASE SETUP - PhotoVerse (Galeria Pessoal)

Passos rápidos:

- Criar um projeto no Firebase Console: https://console.firebase.google.com/
- Habilitar Authentication (Email/Password e/ou Google Sign-In).
- Habilitar Firestore (modo produção) e Firebase Storage.

Arquivo de config:
- Preencha `pages/firebase-config.js` com as credenciais do seu projeto (API key, projectId, etc.).

Regras recomendadas (Firestore) — `Firestore -> Rules`:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /photos/{photoId} {
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
      allow read, list: if request.auth != null && resource.data.uid == request.auth.uid;
      allow delete: if request.auth != null && resource.data.uid == request.auth.uid;
      allow update: if false; // evitar updates diretos (opcional)
    }
  }
}
```

Regras recomendadas (Storage) — `Storage -> Rules`:
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /users/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Observações:
- A página `pages/galeria.html` grava metadados em `collection('photos')` e salva o arquivo em `users/{uid}/...` no Storage. As regras acima garantem que cada usuário só leia/escreva seus próprios arquivos/metadados.
- Não comite `firebase-config.js` com chaves reais em repositórios públicos. Use variáveis de ambiente ou serviços secretos em produção.

Testando localmente:

1. No Firebase Console, copie as configurações do SDK e cole em `pages/firebase-config.js` (substitua os placeholders).
2. Abra `pages/galeria.html` no navegador (ex: arraste para o navegador ou sirva via servidor estático: `python -m http.server` na raiz do projeto).
3. Registre/entre com um usuário e envie imagens. A galeria mostrará somente as imagens do usuário autenticado.
