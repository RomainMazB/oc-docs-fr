# Comportement base de données

- [Purgeable](#purgeable)

Les comportements de modèle sont utilisé pour implémenter des fonctionnalités couramment utilisées.
Contrairement aux [Traits](traits), ils peuvent être implémentés directement dans une classe ou par extension de la
classe. Vous pouvez en apprendre plus sur les comportements [ici](../services/behaviors). 

<a name="purgeable"></a>
## Purgeable

Les propriétés purgées ne seront pas sauvegardé en base de données lorsque celui-ci sera créé ou mis à jour. Pour purger
des propriétés dans un modèle, implémenter le comportement `October.Rain.Database.Behaviors.Purgeable` et déclarer la
propriété `$purgeable` avec pour valeur un tableau contenant le nom des propriétés à purger.

    class User extends Model
    {
        public $implement = [
            'October.Rain.Database.Behaviors.Purgeable'
        ];

        /**
         * @var array List of attributes to purge.
         */
        public $purgeable = [];
    }
    
Vous pouvez aussi implémenter un comportement dynamiquement dans une classe.

    /**
     * Étends le modèle RainLab.User pour implémenter le comportement purgeable.
     */
    RainLab\User\Models\User::extend(function($model) {

        // Implémenter le comportement purgeable dynamiquement
        $model->implement[] = 'October.Rain.Database.Behaviors.Purgeable';
        
        // Déclarer les propriétés purgeable dynamiquement à utiliser par le comportement purgeable
        $model->addDynamicProperty('purgeable', []);
    });

Les propriétés déclarées seront purgées lorsque le modèle sera sauvegardé, avant que les [évènements de la vue](#model-events)
ne soient déclenchés, incluant la validation. Utilisez la méthode `getOriginalPurgeValue` pour retrouver une propriété
qui as été purgée.

    return $user->getOriginalPurgeValue($propertyName);
