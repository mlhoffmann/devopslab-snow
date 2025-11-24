# DevOps Lab - ServiceNow DevOps Integration

Pipeline CI/CD usando **ServiceNow DevOps** (integracao nativa) ao inves de API REST direta.

---

## Diferenca: API REST vs ServiceNow DevOps

| Aspecto | devopslab (API REST) | devopslab-snow (DevOps) |
|---------|----------------------|-------------------------|
| **Metodo** | curl direto para Table API | GitHub Actions oficiais |
| **Aprovacao** | Polling manual (30s) | Callback automatico |
| **Rastreabilidade** | Manual | Automatica (pipeline tracking) |
| **Configuracao** | Simples (usuario/senha) | Requer setup no ServiceNow |
| **Licenca** | Qualquer instancia | Requer licenca DevOps |

---

## Pre-requisitos no ServiceNow

### 1. Licenca DevOps
- Sua instancia precisa ter o modulo **DevOps** habilitado
- Verifique em: System Applications > All Available Applications

### 2. Criar DevOps Tool
1. Acesse: **DevOps > Tool Integrations**
2. Clique em **New**
3. Preencha:
   - **Name**: GitHub Actions
   - **Type**: GitHub
   - **URL**: https://github.com/SEU_USUARIO/devopslab-snow
4. Copie o **Tool ID** gerado (sys_id)

### 3. Criar DevOps Token
1. Acesse: **DevOps > Tool Integrations**
2. Abra a integracao criada
3. Em **Credentials**, clique em **Generate Token**
4. Copie o token gerado

### 4. Configurar Pipeline
1. Acesse: **DevOps > Pipelines**
2. Clique em **New**
3. Configure:
   - **Name**: devopslab-snow
   - **Tool**: GitHub Actions (criado acima)
   - **Repository**: devopslab-snow

---

## GitHub Secrets Necessarios

Configure em **Settings > Secrets and variables > Actions**:

| Secret | Descricao | Exemplo |
|--------|-----------|---------|
| `SERVICENOW_INSTANCE` | URL da instancia (sem https://) | `sua-instancia.service-now.com` |
| `SERVICENOW_DEVOPS_TOOL_ID` | sys_id do Tool Integration | `abc123...` |
| `SERVICENOW_DEVOPS_TOKEN` | Token gerado no ServiceNow | `devops_token_xxx` |

---

## Como Funciona

### Fluxo com ServiceNow DevOps

```
GitHub Actions                          ServiceNow DevOps
     |                                        |
     | 1. Build completo                      |
     |-------- devops-build@v3.1.0 --------->|  Registra build
     |                                        |
     | 2. Solicita Change                     |
     |-------- devops-change@v3.1.0 -------->|  Cria CR automaticamente
     |                                        |
     |        [AGUARDA APROVACAO]             |  Workflow PAUSA aqui
     |                                        |
     |<------- Callback (aprovado) ----------|  ServiceNow notifica GitHub
     |                                        |
     | 3. Deploy executa                      |
     |                                        |
```

### Vantagens do ServiceNow DevOps

1. **Sem polling**: Workflow pausa e recebe callback quando aprovado
2. **Rastreabilidade**: Pipeline visivel no ServiceNow DevOps dashboard
3. **Auditoria**: Registro completo de builds, testes, deploys
4. **Integracao bidirecional**: ServiceNow sabe o estado do pipeline

---

## Estrutura do Projeto

```
devopslab-snow/
├── .github/
│   └── workflows/
│       └── servicenow-devops.yml    # Workflow com DevOps nativo
├── streamlit_app.py                 # Aplicacao Streamlit
├── test_streamlit.py                # Testes automatizados
├── requirements.txt                 # Dependencias Python
├── .gitignore
├── LICENSE
└── README.md
```

---

## GitHub Actions Oficiais da ServiceNow

| Action | Funcao |
|--------|--------|
| `ServiceNow/servicenow-devops-build@v3.1.0` | Registra build no DevOps |
| `ServiceNow/servicenow-devops-change@v3.1.0` | Cria Change Request |
| `ServiceNow/servicenow-devops-test-report@v3.1.0` | Envia resultados de teste |
| `ServiceNow/servicenow-devops-artifact@v3.1.0` | Registra artefatos |
| `ServiceNow/servicenow-devops-sonar@v3.1.0` | Integra com SonarQube |

Documentacao oficial: https://github.com/ServiceNow/servicenow-devops-change

---

## Comparacao de Workflows

### Projeto Original (API REST)
```yaml
# Polling manual
while [ $ATTEMPT -lt $MAX_ATTEMPTS ]; do
  curl -s -X GET ".../change_request/${CR_SYS_ID}"
  sleep 30
done
```

### Este Projeto (DevOps Nativo)
```yaml
# Callback automatico
- uses: ServiceNow/servicenow-devops-change@v3.1.0
  with:
    devops-integration-token: ${{ secrets.SERVICENOW_DEVOPS_TOKEN }}
    # Workflow pausa automaticamente e recebe callback
```

---

## Troubleshooting

### Erro: "Tool integration not found"
- Verifique se o TOOL_ID esta correto
- Confirme que a Tool Integration esta ativa no ServiceNow

### Erro: "Invalid token"
- Regenere o token no ServiceNow
- Atualize o secret no GitHub

### Erro: "DevOps module not available"
- Sua instancia precisa da licenca DevOps
- Verifique com o administrador do ServiceNow

---

## Autor

Marcos Hoffmann

---

**DevOps Lab** - ServiceNow DevOps Integration (nativa)
