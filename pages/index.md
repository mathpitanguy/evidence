---
title: Painel de Vendas
---

```sql categorias
select distinct categoria from vendas.vendas order by categoria
```

```sql regioes
select distinct regiao from vendas.vendas order by regiao
```

```sql status_list
select distinct status from vendas.vendas order by status
```

```sql vendas_filtradas
select
    id,
    vendedor,
    categoria,
    produto,
    regiao,
    valor,
    quantidade,
    round(valor * quantidade, 2) as total,
    data,
    status
from vendas.vendas
where
    ('${inputs.filtro_categoria.value}' = '' or '${inputs.filtro_categoria.value}' = 'Todas' or categoria = '${inputs.filtro_categoria.value}')
    and ('${inputs.filtro_regiao.value}' = '' or '${inputs.filtro_regiao.value}' = 'Todas' or regiao = '${inputs.filtro_regiao.value}')
    and ('${inputs.filtro_status.value}' = '' or '${inputs.filtro_status.value}' = 'Todos' or status = '${inputs.filtro_status.value}')
    and ('${inputs.filtro_vendedor}' = '' or lower(vendedor) like lower('%${inputs.filtro_vendedor}%'))
order by data desc
```

```sql total_pedidos
select count(*) as valor from vendas.vendas
where
    ('${inputs.filtro_categoria.value}' = '' or '${inputs.filtro_categoria.value}' = 'Todas' or categoria = '${inputs.filtro_categoria.value}')
    and ('${inputs.filtro_regiao.value}' = '' or '${inputs.filtro_regiao.value}' = 'Todas' or regiao = '${inputs.filtro_regiao.value}')
    and ('${inputs.filtro_status.value}' = '' or '${inputs.filtro_status.value}' = 'Todos' or status = '${inputs.filtro_status.value}')
    and ('${inputs.filtro_vendedor}' = '' or lower(vendedor) like lower('%${inputs.filtro_vendedor}%'))
```

```sql receita_total
select round(sum(valor * quantidade), 2) as valor from vendas.vendas
where
    ('${inputs.filtro_categoria.value}' = '' or '${inputs.filtro_categoria.value}' = 'Todas' or categoria = '${inputs.filtro_categoria.value}')
    and ('${inputs.filtro_regiao.value}' = '' or '${inputs.filtro_regiao.value}' = 'Todas' or regiao = '${inputs.filtro_regiao.value}')
    and ('${inputs.filtro_status.value}' = '' or '${inputs.filtro_status.value}' = 'Todos' or status = '${inputs.filtro_status.value}')
    and ('${inputs.filtro_vendedor}' = '' or lower(vendedor) like lower('%${inputs.filtro_vendedor}%'))
```

```sql ticket_medio
select round(avg(valor * quantidade), 2) as valor from vendas.vendas
where
    ('${inputs.filtro_categoria.value}' = '' or '${inputs.filtro_categoria.value}' = 'Todas' or categoria = '${inputs.filtro_categoria.value}')
    and ('${inputs.filtro_regiao.value}' = '' or '${inputs.filtro_regiao.value}' = 'Todas' or regiao = '${inputs.filtro_regiao.value}')
    and ('${inputs.filtro_status.value}' = '' or '${inputs.filtro_status.value}' = 'Todos' or status = '${inputs.filtro_status.value}')
    and ('${inputs.filtro_vendedor}' = '' or lower(vendedor) like lower('%${inputs.filtro_vendedor}%'))
```

```sql vendas_por_categoria
select categoria, round(sum(valor * quantidade), 2) as total
from vendas.vendas
group by categoria order by total desc
```

```sql vendas_por_status
select status, count(*) as pedidos
from vendas.vendas
group by status order by pedidos desc
```

# Painel Executivo de Vendas

## Filtros

<Dropdown data={categorias} name=filtro_categoria value=categoria title="Todas as Categorias" label="Categoria"/>
<Dropdown data={regioes} name=filtro_regiao value=regiao title="Todas as Regiões" label="Região"/>
<Dropdown data={status_list} name=filtro_status value=status title="Todos os Status" label="Status"/>
<TextInput name=filtro_vendedor placeholder="Buscar por vendedor..." label="Vendedor"/>

---

## Resumo

<BigValue data={total_pedidos} value=valor title="Total de Pedidos"/>
<BigValue data={receita_total} value=valor title="Receita Total (R$)" fmt=num2/>
<BigValue data={ticket_medio} value=valor title="Ticket Médio (R$)" fmt=num2/>

---

## Vendas por Categoria e Status

<Grid cols=2>

<BarChart
    data={vendas_por_categoria}
    x=categoria
    y=total
    title="Receita por Categoria (R$)"
    swapXY=true
/>

<BarChart
    data={vendas_por_status}
    x=status
    y=pedidos
    title="Pedidos por Status"
/>

</Grid>

---

## Detalhamento

<DataTable data={vendas_filtradas} rows=10 search=true>
    <Column id=id title="ID"/>
    <Column id=vendedor title="Vendedor"/>
    <Column id=categoria title="Categoria"/>
    <Column id=produto title="Produto"/>
    <Column id=regiao title="Região"/>
    <Column id=valor title="Valor Unit." fmt=num2/>
    <Column id=quantidade title="Qtd."/>
    <Column id=total title="Total (R$)" fmt=num2/>
    <Column id=data title="Data"/>
    <Column id=status title="Status"/>
</DataTable>
