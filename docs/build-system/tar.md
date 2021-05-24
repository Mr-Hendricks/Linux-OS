<package-info :package="package" showsbu2></package-info>

<script>
		new Vue({
		el: '#main',
		data: { package: {} },
		mounted: function () {
				this.getPackage('tar');
		},
		methods: {
			getPackage: function(name) {
					getPackage(name)
					.then(response => this.package = response);
			},
		}
  })
</script>

## Настройка


```bash
FORCE_UNSAFE_CONFIGURE=1  \
./configure --prefix=/usr
```

## Сборка


```bash
make
```
## Тестирование

```bash
make check
```

## Установка

```bash
make install
```