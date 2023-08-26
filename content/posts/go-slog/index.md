---
title: "Go: les logs avec slog"
date: 2023-08-21T18:47:52+02:00
draft: false
comment: false
---

Depuis Go 1.21, un nouveau package est disponible pour logger en Go. J'attendais ce nouveau package avec impatience, il permet beaucoup de chose et rend la lib de base de Go encore plus intéressante. le package log reste en place, cependant slog est bien plus intéressant.  

Ce nouveau logger permet d'avoir différents niveaux de logs, un mode debug. Il permet également de gérer la sortie des logs et d'avoir différents mode, mode classique ou log en JSON.


## Les fonctions de base

Pour logger avec slog, il suffit d'utiliser les fonctions Info,Warn,Error ou Debug du package slog.  

Exemple:
```go
package main
import (
    "log/slog"
)
func main(){
	slog.Info("Hello info")
	slog.Warn("Hello Warning")
	slog.Error("Hello Error")
	slog.Debug("Hello Debug")
}
```

Le résultat sera:
>2023/08/21 18:59:30 INFO Hello info  
>2023/08/21 18:59:30 WARN Hello Warning  
>2023/08/21 18:59:30 ERROR Hello Error  

Notons que le mode debug n'est pas activé.

## La création d'un système de log avancé

Il est important de savoir que la base de slog est basé sur du JSON, à partir ou l'on modifie le logger, on se retrouve avec des logs au format clé-valeur.

Plutot que faire un paragraphe par élément, je trouve plus simple de commencer par un seul gros bloc de code:

```go
package main
import (
    "log/slog"
)
func main(){
	logger := slog.New(slog.NewTextHandler(os.Stdout, &slog.HandlerOptions{
		Level:     slog.LevelDebug, // niveau des Logs, le nom est slog.Level + niveau souhaité (Info,Error,Warn,Debug)
		AddSource: true,            // permet d'afficher l'emplacement de l'erreur du log
		ReplaceAttr: func(groups []string, a slog.Attr) slog.Attr { // redéfinition des clés:
			if a.Key == slog.TimeKey {
				return slog.String("timeCustom", a.Value.Time().Format("2006-01-02 15:04:05")) // modifie la valeur du temps pour afficher l'heure comme sur les logs normaux
			}
			if a.Key == slog.LevelKey { // renomme la clé level
				a.Key = "lvl"
			}
			return a
		},
	}))
	logger.Debug("Il s'agit d'une erreur et la source de l'erreur est affichée")
}
```

La sortie des logs peux également se faire intégralement en JSON (cette fois je ne modifie pas le handle de base donc il vaut nil):
```go
package main
import (
    "log/slog"
)
func main(){
	logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
	logger.Warn("Message de log")
}
```

## Créer des clés

Dans les messages de log, il est possible d'ajouter de nouvelles clés grace aux fonctions ``slog.TypeClé``, ces clés peuvent être groupée grace à la fonction ```slog.Group```

```go
package main
import "log/slog"
func main(){
    logger := slog.New(slog.NewTextHandler(os.Stdout, nil))
	logger.Info("Texte du message", "Nombre", 100, "bool", true, "String", "Bonjour!") // méthode 1: param: msg + clé1 + val1 + clé 2 + val 2 +...
	logger.Info("Texte du message", slog.Group("Nom de la clé du groupe", slog.Int("Nombre", 100), slog.Bool("Bool", true), slog.String("String val", "Bonjour!")))
	// dans le Group, les valeurs sont affiché en nomgroupe.clé
}
```

On peux également ajouter un attribut à chaque log:

```go
package main
import "log/slog"
func main(){
    logger := slog.New(slog.NewTextHandler(os.Stdout, nil).WithAttrs([]slog.Attr{
        slog.String("Ajout d'un attribut aux logs", "Valeur"),
    }))
	logger.Info("Texte du message")
	// dans le Group, les valeurs sont affiché en nomgroupe.clé
}
```

## Adapter l'affichage des structures

Pour les structures, on peux les afficher et les récupérer en JSON grâce à une interface de slog:

```go
package main

import (
	"fmt"
	"log/slog"
	"os"
)

type Point struct {
	X int
	Y int
}

func (p Point) LogValue() slog.Value {
	return slog.GroupValue(
		slog.Int("X", p.X),
		slog.Int("Y", p.Y),
	)
}

func main() {
	logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
	pt := Point{X: 50, Y: 30}
	logger.Info("Utilisation de la fonction interface de slog", "Point:", pt)
}
```

## Redéfinir le logger par défaut

Un des avantages de slog est de pouvoir redéfinir le logger par défaut, ainsi tous les futurs appels à slog.Info prendront les paramètres personnalisés, les logs du package log seront également impactés par ces modifications.

```go
package main

import (
	"fmt"
	"log"
	"log/slog"
	"os"
)

type Point struct {
	X int
	Y int
}

func (p Point) String() string {
	return fmt.Sprintf("type Point: X=%d,Y=%d", p.X, p.Y)
}

func main() {
	logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
	pt := Point{X: 50, Y: 30}
	slog.SetDefault(logger) // désormais log et slog utiliserons notre logger
	slog.Info("Log de point" + pt.String())
	log.Println("Point: ", pt)
}
```

## Modification du formattage du texte et passage du texte en couleur

En utilisant le principe d'interface et d'héritage de Go, il est possible de redéfinir les messages de log:

> L'exemple suivant est complètement inspiré de ce lien: [https://betterstack.com/community/guides/logging/logging-in-go/](https://betterstack.com/community/guides/logging/logging-in-go/)

```go
package main

import (
	"context"
	"encoding/json"
	"fmt"
	"io"
	"log"
	"log/slog"
	"os"

	"github.com/fatih/color"
)

type MyCustomHandlerOptions struct {
	SlogOpts slog.HandlerOptions
}

type MyCustomHandler struct {
	slog.Handler
	l *log.Logger
}

func (h *MyCustomHandler) Handle(ctx context.Context, r slog.Record) error {
	level := "[" + r.Level.String() + "] :"
	timeStr := r.Time.Format("2006-01-02 15:05:05.000")

	fields := make(map[string]interface{}, r.NumAttrs())
	r.Attrs(func(a slog.Attr) bool {
		fields[a.Key] = a.Value.Any()
		return true
	})

	b, err := json.MarshalIndent(fields, "", "  ")
	if err != nil {
		return err
	}

	header := fmt.Sprint(level, r.Message)

	switch r.Level {
	case slog.LevelDebug:
		header = color.MagentaString(header)
	case slog.LevelInfo:
		header = color.WhiteString(header)
	case slog.LevelWarn:
		header = color.YellowString(header)
	case slog.LevelError:
		header = color.RedString(header)
	}
	h.l.Println(color.CyanString(timeStr), header, color.WhiteString(string(b)))

	return nil
}

func NewPrettyHandler(
	out io.Writer,
	opts MyCustomHandlerOptions,
) *MyCustomHandler {
	h := &MyCustomHandler{
		Handler: slog.NewJSONHandler(out, &opts.SlogOpts),
		l:       log.New(out, "", 0),
	}
	return h
}

func main() {
	opts := MyCustomHandlerOptions{
		SlogOpts: slog.HandlerOptions{
			Level: slog.LevelDebug,
		},
	}
	logger := slog.New(NewPrettyHandler(os.Stdout, opts))
	logger.Debug(
		"executing database query",
		slog.String("query", "SELECT * FROM users"),
	)
	logger.Info("image upload successful", slog.String("image_id", "39ud88"))
	logger.Warn(
		"storage is 90% full",
		slog.String("available_space", "900.1 MB"),
	)
	logger.Error(
		"An error occurred while processing the request",
		slog.String("url", "https://example.com"),
	)
}
```

On peux également se passer du module de couleurs avec en remplacant la fonction Handle avec la suivante:

> Attention cependant, les caractères de couleur seront afficher si il ne sont pas interprétés (lors du renvoi vers un fichier par exemple)

```go
func (h *MyCustomHandler) Handle(ctx context.Context, r slog.Record) error {
	level := "[" + r.Level.String() + "] "
	timeStr := "\033[36m" + r.Time.Format("2006-01-02 15:05:05") + " \033[0m"

	fields := make(map[string]interface{}, r.NumAttrs())
	r.Attrs(func(a slog.Attr) bool {
		fields[a.Key] = a.Value.Any()
		return true
	})

	b, err := json.MarshalIndent(fields, "", "")
	if err != nil {
		return err
	}

	header := fmt.Sprint(level, r.Message)

	switch r.Level {
	case slog.LevelDebug:
		header = "\033[35m" + header + "\033[0m"
	case slog.LevelInfo:
		header = "\033[97m" + header + "\033[0m"
	case slog.LevelWarn:
		header = "\033[33m" + header + "\033[0m"
	case slog.LevelError:
		header = "\033[31m" + header + "\033[0m"
	}
	msg := "\033[97m" + string(b) + " \033[0m"
	h.l.Println(timeStr, header, msg)
	return nil
}

```

Un exemple similaire est disponible [ici](https://dusted.codes/creating-a-pretty-console-logger-using-gos-slog-package)





## Sources

- [https://programmingpercy.tech/blog/structured-logging-in-go-using-std-lib-slog/#custom-attributes-andgroups](https://programmingpercy.tech/blog/structured-logging-in-go-using-std-lib-slog/#custom-attributes-andgroups)  
- [https://www.youtube.com/watch?v=kgkQZnh7BbI](https://www.youtube.com/watch?v=kgkQZnh7BbI)
- [https://www.gopherguides.com/articles/golang-slog-package](https://www.gopherguides.com/articles/golang-slog-package)
- [https://thedevelopercafe.com/articles/logging-in-go-with-slog-a7bb489755c2](https://thedevelopercafe.com/articles/logging-in-go-with-slog-a7bb489755c2)
- [https://dusted.codes/creating-a-pretty-console-logger-using-gos-slog-package](https://dusted.codes/creating-a-pretty-console-logger-using-gos-slog-package)