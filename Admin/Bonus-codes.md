# Бонус-коды


## Пример
~~~python

BONUS_CODES = [

    # This code could be published somewhere in advertising
    # Could be applied only once
    # Grants  group for 1 year
    {
        'name': 'ReleasePromo2019',
        'prefix': 'ReleasePromo2019',
        'group': 'AlcorPromo',
        'days': 365,
        'expires': "2020-12-31",
        # 'repeat': 10  # Repeat period in days
    },

    # This code is internal (started with underscore). It will not be applied if entered in profile
    # Grants group AlcorPromo forever
    {
        'name': '_forever',
        'prefix': '_forever',
        'group': 'AlcorPromo',
    },

    # This code is internal (started with underscore). It will not be applied if entered in profile
    # Grants 1mo of HIGHEST already applied group (by weight/_price/maxindicators)
    # Could be repeated every day (but only by admin)
    {
        'name': '_1mo',
        'prefix': '_1mo',
        'days': 31,
        'repeat': 1,
    },

    # This code is provider-specific
    # provider can generate codes on his side
    # okerr can verify codes based on pre-shared secret
    {
        'name': 'provider1',
        'prefix': 'provider1:',
        'group': 'AlcorPromo',
        'days': 31,
        'verification': 'hmac:sha256',
        'secret': '1234567890abcdef'
    },

    # This code is provider-specific
    # provider can generate codes on his side
    # okerr can verify codes based on pre-shared secret
    {
        'name': 'provider2',
        'prefix': 'provider2:',
        'group': 'AlcorPromo',
        'days': 31,
        'discard_if_failed': 31,
        'reactivation': 2,
        'verification': 'url:200',
        'verify_url': 'http://test.sysattack.com/codes/{CODE}',
    },

]

~~~