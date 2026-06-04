# ADR 0004: Frontend CSR (Client-Side Rendering)

### Por quê CSR é a melhor escolha:

1. **Arquitetura de Micro Frontends**
   - Estamos usando Native Federation, que carrega módulos dinamicamente no cliente
   - SSR complexificaria a orquestração dos MFEs remotos
   - Cada MFE seria independente e difícil de renderizar no servidor

2. **SPA + PWA**
   - A aplicação é uma SPA com suporte a PWA (Service Worker no Shell)
   - O objetivo é cache offline e experiência de app nativo
   - PWA + SSR adiciona complexidade desnecessária

3. **Estado compartilhado via Signals**
   - Usa **Signal Store** global para compartilhar estado entre MFEs
   - SSR seria stateless — perderia o contexto compartilhado
   - O estado precisa estar no cliente de qualquer forma

4. **Natureza da aplicação**
   - **Vinho Notas** é uma **ferramenta/app**, não é um site público de conteúdo
   - SEO não é uma prioridade (é autenticada)
   - Foco é em performance de interação, não em indexação

### Ganhos com CSR:

✅ Builds mais rápidos (sem renderização no servidor)  
✅ Simplicidade na infraestrutura (Nginx stateless para cada MFE)  
✅ Melhor experiência offline (PWA)  
✅ Independência entre MFEs  
✅ Alinhado com 12-factor app (stateless frontend)

### Perdas:

❌ Carregamento inicial mais longo (mitigado com lazy loading dos MFEs)  
❌ Sem SEO (não é necessário neste caso)

---

## **⚡ Otimizações para melhorar performance inicial:**

```typescript
// app.config.ts do Shell
import { provideRouter } from '@angular/router';
import { provideZonelessChangeDetection } from '@angular/core';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes, 
      withPreloading(PreloadAllModules) // ← precarrega MFEs de background
    ),
    provideZonelessChangeDetection(), // ← mais rápido
    provideHttpClient(
      withInterceptors([authInterceptor])
    ),
  ]
};
```

---

## **📋 Resumo final:**

| Aspecto | Sua Escolha |
|---|---|
| **Renderização** | **CSR** |
| **Infraestrutura** | Nginx Alpine por MFE (já configurado) |
| **Otimizações** | Lazy loading + preload + Signals + change detection zoneless |
| **Deployment** | Independente por MFE (sem redeploy do Shell) |
