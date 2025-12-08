# 🔥 Guia Prático: Firebase para PhotoVerse

Seu projeto já tem o código pronto (`pages/galeria.html`). Agora precisa configurar o Firebase Console.

## ✅ Checklist de Configuração

### 1. Habilitar Autenticação Anônima
1. Acesse: https://console.firebase.google.com/
2. Selecione o projeto: **photoverse-53852**
3. Menu lateral → **Authentication**
4. Aba **Sign-in method**
5. Localize **Anonymous** (Anônimo)
6. Clique no switch para ativar ✅
7. Clique **Save** (Salvar)

### 2. Criar/Habilitar Firestore Database
1. Menu lateral → **Firestore Database**
2. Se não existir, clique **Create database**
3. Escolha a localização (recomendado: usa-east1)
4. Modo de segurança: **Start in production mode** (mais seguro)
5. Clique **Create**

### 3. Configurar Regras do Firestore
1. No Firestore → Aba **Rules**
2. Delete o conteúdo padrão e cole:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /photos/{photoId} {
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
      allow read, list: if request.auth != null && resource.data.uid == request.auth.uid;
      allow delete: if request.auth != null && resource.data.uid == request.auth.uid;
    }
  }
}
```

3. Clique **Publish** (Publicar)

### 4. Criar/Habilitar Storage
1. Menu lateral → **Storage**
2. Se não existir, clique **Get Started**
3. Escolha a localização (recomendado: usa-east1)
4. Clique **Next** e depois **Done**

### 5. Configurar Regras do Storage
1. No Storage → Aba **Rules**
2. Delete o conteúdo padrão e cole:

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

3. Clique **Publish** (Publicar)

## 🚀 Testar Localmente

```bash
# Na raiz do projeto
python3 -m http.server 8000
```

Depois abra no navegador:
```
http://localhost:8000/pages/galeria.html
```

### O que esperar:
1. Aparece a seção "Enviar Fotos" (significa que autenticou anonimamente)
2. Selecione uma imagem e clique "Enviar"
3. Aguarde "Upload concluído"
4. A imagem aparece na galeria abaixo

### Verificar no Firebase Console:
- **Storage**: deve haver pasta `users/{uid}/` com a imagem
- **Firestore**: deve haver documento em `photos/` com metadados (url, name, uid, createdAt)

## ⚠️ Se der erro:

### "Firebase: Error (auth/configuration-not-found)"
→ Volte ao passo 1 e ative **Anonymous Authentication**

### "Permission denied" ou "PERMISSION_DENIED"
→ Verifique as regras do Firestore e Storage (passos 3 e 5)

### Imagem não aparece na galeria
→ Verifique o Console do navegador (F12 → Console) para mensagens de erro

---

**Pronto!** Suas fotos estarão armazenadas no Firebase Storage e os metadados no Firestore.
