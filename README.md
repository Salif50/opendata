### Cahier des Charges Révisé pour la Mise en Place d'une API Open Data en Utilisant Django Rest Framework et GitHub

#### Contexte et Objectifs

La République de Guinée manque actuellement d'une API Open Data permettant d'accéder à des informations géographiques et démographiques structurées. Ce projet vise à combler ce manque en développant une API RESTful utilisant Django Rest Framework (DRF) et en mettant en place un système de versioning via GitHub pour faciliter la collaboration et le suivi des modifications.

L'API doit permettre de récupérer des données sur :
- Les régions naturelles et administratives.
- Les préfectures.
- Les communes urbaines et rurales.
- Les villages et quartiers.
- Les langues parlées par région administrative.

#### Public Cible

Ce projet est destiné aux étudiants débutants en développement web, en particulier ceux qui souhaitent se familiariser avec les concepts de développement d'API, de gestion de versions, et de collaboration en équipe.

---

### Modélisation des Données

#### Structure Hiérarchique des Modèles

Voici la structure des modèles Django en tenant compte des relations hiérarchiques spécifiées :

1. **RegionNaturelle** :
   - Une région naturelle peut contenir plusieurs régions administratives.
   - Exemple : "Basse-Guinée", "Haute-Guinée", etc.

2. **RegionAdministrative** :
   - Une région administrative appartient à une région naturelle.
   - Une région administrative peut contenir plusieurs préfectures, communes urbaines, et langues.
   - Exemple : "Kindia" (appartient à la région naturelle "Basse-Guinée").

3. **Prefecture** :
   - Une préfecture appartient à une région administrative.
   - Exemple : "Coyah" (appartient à la région administrative "Kindia").

4. **CommuneUrbaine** :
   - Une commune urbaine appartient à une région administrative.
   - Une commune urbaine peut contenir plusieurs communes rurales et quartiers.
   - Exemple : "Kindia Centre" (appartient à la région administrative "Kindia").

5. **CommuneRurale** :
   - Une commune rurale appartient à une commune urbaine.
   - Une commune rurale peut contenir plusieurs villages.
   - Exemple : "Sangoléya" (appartient à la commune urbaine "Kindia Centre").

6. **Village** :
   - Un village appartient à une commune rurale.
   - Exemple : "Sangoléya Centre" (appartient à la commune rurale "Sangoléya").

7. **Quartier** :
   - Un quartier appartient à une commune urbaine.
   - Exemple : "Quartier Madina" (appartient à la commune urbaine "Kindia Centre").

8. **Langue** :
   - Une langue est rattachée à une région administrative.
   - Exemple : "Soussou" (rattachée à la région administrative "Kindia").

---

#### Modèles Django

Voici la définition des modèles Django :

```python
from django.db import models

class RegionNaturelle(models.Model):
    nom = models.CharField(max_length=100, unique=True)

    def __str__(self):
        return self.nom

class RegionAdministrative(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    region_naturelle = models.ForeignKey(RegionNaturelle, on_delete=models.CASCADE, related_name='regions_administratives')

    def __str__(self):
        return self.nom

class Prefecture(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    region_administrative = models.ForeignKey(RegionAdministrative, on_delete=models.CASCADE, related_name='prefectures')

    def __str__(self):
        return self.nom

class CommuneUrbaine(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    region_administrative = models.ForeignKey(RegionAdministrative, on_delete=models.CASCADE, related_name='communes_urbaines')

    def __str__(self):
        return self.nom

class CommuneRurale(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    commune_urbaine = models.ForeignKey(CommuneUrbaine, on_delete=models.CASCADE, related_name='communes_rurales')

    def __str__(self):
        return self.nom

class Village(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    commune_rurale = models.ForeignKey(CommuneRurale, on_delete=models.CASCADE, related_name='villages')

    def __str__(self):
        return self.nom

class Quartier(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    commune_urbaine = models.ForeignKey(CommuneUrbaine, on_delete=models.CASCADE, related_name='quartiers')

    def __str__(self):
        return self.nom

class Langue(models.Model):
    nom = models.CharField(max_length=100, unique=True)
    region_administrative = models.ForeignKey(RegionAdministrative, on_delete=models.CASCADE, related_name='langues')

    def __str__(self):
        return self.nom
```

---

### Fonctionnalités Requises

1. **API Open Data** :
   - Récupérer la liste des régions naturelles.
   - Récupérer la liste des régions administratives par région naturelle.
   - Récupérer la liste des préfectures par région administrative.
   - Récupérer la liste des communes urbaines par région administrative.
   - Récupérer la liste des communes rurales par commune urbaine.
   - Récupérer la liste des villages par commune rurale.
   - Récupérer la liste des quartiers par commune urbaine.
   - Récupérer la liste des langues par région administrative.

2. **Gestion des Données** :
   - Les données doivent être stockées dans une base de données PostgreSQL.
   - Les modèles Django doivent être créés pour chaque entité.
   - Les données doivent être accessibles via des endpoints API.

3. **Versioning et Collaboration** :
   - Utilisation de GitHub pour le versioning du code.
   - Mise en place de branches pour chaque fonctionnalité.
   - Utilisation de Pull Requests pour la revue de code.
   - Documentation du code et des endpoints API.

---

### Étapes du Projet

1. **Initialisation du Projet** :
   - Créer un nouveau projet Django.
   - Configurer Django Rest Framework.
   - Configurer PostgreSQL comme base de données.

2. **Création des Modèles** :
   - Définir les modèles Django pour chaque entité.
   - Créer et appliquer les migrations.

3. **Développement des Endpoints API** :
   - Créer des sérialiseurs pour chaque modèle.
   - Développer des viewsets et des routers pour exposer les endpoints API.
   - Tester les endpoints avec des outils comme Postman.

4. **Versioning avec GitHub** :
   - Initialiser un dépôt GitHub pour le projet.
   - Créer des branches pour chaque fonctionnalité.
   - Utiliser des Pull Requests pour fusionner le code.
   - Documenter le processus de collaboration.

5. **Documentation** :
   - Documenter les modèles, les sérialiseurs, les viewsets, et les endpoints API.
   - Créer un fichier README.md pour expliquer comment installer et utiliser le projet.

---

### Exemple de Code

#### Sérialiseurs

```python
from rest_framework import serializers
from .models import RegionNaturelle, RegionAdministrative, Prefecture, CommuneUrbaine, CommuneRurale, Village, Quartier, Langue

class RegionNaturelleSerializer(serializers.ModelSerializer):
    class Meta:
        model = RegionNaturelle
        fields = '__all__'

class RegionAdministrativeSerializer(serializers.ModelSerializer):
    class Meta:
        model = RegionAdministrative
        fields = '__all__'

class PrefectureSerializer(serializers.ModelSerializer):
    class Meta:
        model = Prefecture
        fields = '__all__'

class CommuneUrbaineSerializer(serializers.ModelSerializer):
    class Meta:
        model = CommuneUrbaine
        fields = '__all__'

class CommuneRuraleSerializer(serializers.ModelSerializer):
    class Meta:
        model = CommuneRurale
        fields = '__all__'

class VillageSerializer(serializers.ModelSerializer):
    class Meta:
        model = Village
        fields = '__all__'

class QuartierSerializer(serializers.ModelSerializer):
    class Meta:
        model = Quartier
        fields = '__all__'

class LangueSerializer(serializers.ModelSerializer):
    class Meta:
        model = Langue
        fields = '__all__'
```

#### ViewSets

```python
from rest_framework import viewsets
from .models import RegionNaturelle, RegionAdministrative, Prefecture, CommuneUrbaine, CommuneRurale, Village, Quartier, Langue
from .serializers import RegionNaturelleSerializer, RegionAdministrativeSerializer, PrefectureSerializer, CommuneUrbaineSerializer, CommuneRuraleSerializer, VillageSerializer, QuartierSerializer, LangueSerializer

class RegionNaturelleViewSet(viewsets.ModelViewSet):
    queryset = RegionNaturelle.objects.all()
    serializer_class = RegionNaturelleSerializer

class RegionAdministrativeViewSet(viewsets.ModelViewSet):
    queryset = RegionAdministrative.objects.all()
    serializer_class = RegionAdministrativeSerializer

class PrefectureViewSet(viewsets.ModelViewSet):
    queryset = Prefecture.objects.all()
    serializer_class = PrefectureSerializer

class CommuneUrbaineViewSet(viewsets.ModelViewSet):
    queryset = CommuneUrbaine.objects.all()
    serializer_class = CommuneUrbaineSerializer

class CommuneRuraleViewSet(viewsets.ModelViewSet):
    queryset = CommuneRurale.objects.all()
    serializer_class = CommuneRuraleSerializer

class VillageViewSet(viewsets.ModelViewSet):
    queryset = Village.objects.all()
    serializer_class = VillageSerializer

class QuartierViewSet(viewsets.ModelViewSet):
    queryset = Quartier.objects.all()
    serializer_class = QuartierSerializer

class LangueViewSet(viewsets.ModelViewSet):
    queryset = Langue.objects.all()
    serializer_class = LangueSerializer
```

#### Routers

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import RegionNaturelleViewSet, RegionAdministrativeViewSet, PrefectureViewSet, CommuneUrbaineViewSet, CommuneRuraleViewSet, VillageViewSet, QuartierViewSet, LangueViewSet

router = DefaultRouter()
router.register(r'regions-naturelles', RegionNaturelleViewSet)
router.register(r'regions-administratives', RegionAdministrativeViewSet)
router.register(r'prefectures', PrefectureViewSet)
router.register(r'communes-urbaines', CommuneUrbaineViewSet)
router.register(r'communes-rurales', CommuneRuraleViewSet)
router.register(r'villages', VillageViewSet)
router.register(r'quartiers', QuartierViewSet)
router.register(r'langues', LangueViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

---

### Conclusion

Ce projet offre une excellente opportunité pour les étudiants de se familiariser avec le développement d'API, la gestion de versions, et la collaboration en équipe. En suivant ce cahier des charges, ils pourront créer une API Open Data utile pour la communauté tout en acquérant des compétences pratiques en développement web.
