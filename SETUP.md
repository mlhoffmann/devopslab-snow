# Guia de Configuração - ServiceNow DevOps

Configuração do projeto devopslab-snow com a instância demo ServiceNow.

**Instância**: `demoalectriallwfzu127848.service-now.com`

---

## Passo 1: Criar repositório no GitHub

1. Acesse: https://github.com/new
2. Preencha:
   - **Nome**: `devopslab-snow`
   - **Visibilidade**: Public
   - **NÃO** inicialize com README
3. Clique em **Create repository**

---

## Passo 2: Fazer push do código

```bash
cd /Users/marcos.hoffmann/Dropbox/devops/devopslab-snow
git remote add origin https://github.com/mlhoffmann/devopslab-snow.git
git push -u origin main
```

---

## Passo 3: Configurar ServiceNow DevOps Tool Integration

### A. Criar Tool Integration

1. Acesse: https://demoalectriallwfzu127848.service-now.com
2. No filtro de navegação, digite: **DevOps Tool Integration**
3. Clique em **New**
4. Preencha:
   - **Tool label**: `GitHub`
   - **Integration version**: `1`
   - **User-created Integrations**: ✅ Marcar
   - **Active**: ✅ Marcar
5. Clique em **Submit**

### B. Copiar sys_id (Tool ID)

Após criar:
1. Clique com botão direito no cabeçalho do formulário
2. Selecione **Copy sys_id**
3. Salve esse valor - será o `SERVICENOW_DEVOPS_TOOL_ID`

Ou copie da URL:
```
https://demoalectriallwfzu127848.service-now.com/.../devops_integration.do?sys_id=XXXXXXXXXX
                                                                                   ↑
                                                                            Este é o Tool ID
```

### C. Gerar Token de Integração

**IMPORTANTE**: Sua instância pode não ter o recurso de gerar token automaticamente.

#### Opção 1: Token automático (se disponível)
1. Abra o registro da Tool Integration criada
2. Procure por **Integration Token** ou **Credentials**
3. Clique em **Generate Token**
4. Copie o token - será o `SERVICENOW_DEVOPS_TOKEN`

#### Opção 2: Usar credenciais básicas (fallback)
Se não conseguir gerar token, vamos usar autenticação básica:
- Use usuário e senha da instância demo
- Configure no workflow usando Basic Auth

---

## Passo 4: Adicionar GitHub Secrets

Acesse: https://github.com/mlhoffmann/devopslab-snow/settings/secrets/actions

Adicione os secrets:

| Secret | Valor | Descrição |
|--------|-------|-----------|
| `SERVICENOW_INSTANCE` | `demoalectriallwfzu127848.service-now.com` | URL da instância demo |
| `SERVICENOW_DEVOPS_TOOL_ID` | `[sys_id copiado]` | Tool ID do passo 3B |
| `SERVICENOW_DEVOPS_TOKEN` | `[token gerado]` | Token do passo 3C |

**OU** (se não conseguir token):

| Secret | Valor | Descrição |
|--------|-------|-----------|
| `SERVICENOW_INSTANCE` | `demoalectriallwfzu127848.service-now.com` | URL da instância demo |
| `SERVICENOW_USERNAME` | `admin` | Usuário da instância demo |
| `SERVICENOW_PASSWORD` | `[senha]` | Senha da instância demo |

---

## Passo 5: Testar a Integração

### Opção A: Se tiver ServiceNow DevOps completo

Execute o workflow:
1. Acesse: https://github.com/mlhoffmann/devopslab-snow/actions
2. Clique em **ServiceNow DevOps Pipeline**
3. Clique em **Run workflow**
4. Clique no botão verde **Run workflow**

### Opção B: Se não tiver DevOps completo (fallback para API REST)

Vamos criar um workflow alternativo usando API REST (como o projeto original):

```bash
# Será criado automaticamente se o workflow DevOps falhar
```

---

## Troubleshooting

### Erro: "DevOps module not available"

**Causa**: A instância demo pode não ter o módulo DevOps completo instalado.

**Solução**: Use o projeto original `devopslab` com API REST - funciona em qualquer instância.

### Erro: "Cannot generate token"

**Causa**: A instância demo pode não ter permissões para gerar tokens.

**Solução**: Use autenticação básica (usuário/senha) no workflow.

### Erro: "Tool integration not found"

**Causa**: O TOOL_ID está incorreto.

**Solução**: Verifique se copiou o sys_id correto do registro criado.

---

## Próximos Passos

Após configurar:
1. Execute o workflow manualmente
2. Verifique se a CR é criada no ServiceNow
3. Compare com o projeto original (`devopslab`)

---

## Comparação Final

| Característica | devopslab (API REST) | devopslab-snow (DevOps) |
|----------------|----------------------|-------------------------|
| **Status** | ✅ Funcionando | 🔬 Experimental |
| **Instância** | mlhsouza.service-now.com | demoalectriallwfzu127848.service-now.com |
| **Complexidade** | Simples | Requer configuração avançada |
| **Recomendação** | **Produção** | Testes/Demo |

---

**Recomendação**: Mantenha o projeto `devopslab` funcionando. Use `devopslab-snow` apenas para testes e aprendizado.
