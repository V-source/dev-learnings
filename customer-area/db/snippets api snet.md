- **actualizar base de datos**:
```bash 
docker compose exec backend php artisan migrate:fresh --seed
```

- **crud de facturas**:
1.
```bash
docker compose backend php artisan tinker

## Ó tambien puedes:

docker compose backend php artisan tinker --execute="codigo php"
```
2.

#### Crear Facturas a cada servicio de un cliente (aleatorias)
> 30 en este caso.

```php
App\Models\Cliente::find(10)->servicios->each(fn ($s) => App\Models\Factura::factory(30)->for($s)->create())

```

---


#### para crear solo facturas pendientes

```php

App\Models\Cliente::find(10)->servicios->each(fn ($s) => App\Models\Factura::factory(30)->for($s)->pendiente()->create())
```

#### ELIMINAR FACTURAS (TODAS)

```php
App\Models\Cliente::find(10)->servicios->each(fn ($s)=>$s->facturas->each->delete())
```


#### CREAR FACTURAS PAGADAS

```php
App\Models\Cliente::find(10)->servicios->each(fn ($s) => App\Models\Factura::factory(30)->for($s)->state(["estado"=>"Pagado"])->create())
```

#### CREAR REFERIDOS
```php
use App\Enums\ReferidoStatus;
use App\Models\{Cliente, Referido};
use Illuminate\Database\Eloquent\Factories\Sequence;

$sequence = new Sequence(ReferidoStatus::ELIMINADO, ReferidoStatus::VENCIDO, ReferidoStatus::CONFIRMADO);
Referido::factory(30)->hasAttached(Cliente::find(26), fn () => ['status' => $sequence()])->create();
```
