# 配置加载

## 方法说明

```go
// MustLoadConfig 加载配置，异常时 panic
func MustLoadConfig[T any](files ...string) T
```
`luchen` 提供了配置文件加载辅助方法，支持泛型，内部使用 `github.com/spf13/viper` 来加载配置文件。读取配置异常时程序会 panic。

支持加载多个配置文件，当多个文件配置存在相同配置 key 时，后加载的将会覆盖之前的配置。

## 参考示例

```go
package config

import (
	"flag"
	"os"
	"path/filepath"

	"github.com/fengjx/go-halo/fskit"
	"github.com/fengjx/luchen"
	"github.com/fengjx/luchen/env"
	"github.com/fengjx/luchen/log"
	"go.uber.org/zap"
)

var appConfig AppConfig

func init() {
	configArg := flag.String("c", "", "custom config file path")
	flag.Parse()
	appCfg := "conf/app.yml"
	if !env.IsDev() {
		exePath, err := os.Executable()
		if err != nil {
			log.Panic("can not get exec file path", zap.Error(err))
		}
		appCfg = filepath.Join(filepath.Dir(exePath), "conf/app.yml")
	}
	log.Infof("app conf: %s", appCfg)
	appConfigFile, err := fskit.Lookup(appCfg, 5)
	if err != nil {
		log.Panic("config file not found", zap.String("path", appCfg), zap.Error(err))
	}
	configs := []string{appConfigFile}
	var configFile string
	if configArg != nil {
		configFile = *configArg
	}
	if configFile == "" {
		envConfig := os.Getenv("APP_CONFIG")
		if envConfig != "" {
			configFile = envConfig
		}
	}
	if configFile != "" {
		configFile, err = fskit.Lookup(configFile, 5)
		if err != nil {
			log.Panic("config file not found", zap.String("path", configFile), zap.Error(err))
		}
		log.Infof("load custom config file: %s", configFile)
		configs = append(configs, configFile)
	}
	appConfig = luchen.MustLoadConfig[AppConfig](configs...)
}

type AppConfig struct {
	Server Server                  `json:"server"`
	Auth   AuthConfig              `json:"auth"`
	DB     map[string]*DbConfig    `json:"db"`
	Redis  map[string]*RedisConfig `json:"redis"`
}

type Server struct {
	HTTP HTTPServerConfig `json:"http"`
	GRPC GRPCServerConfig `json:"grpc"`
}

type AuthConfig struct {
	Version string `json:"version"`
	Secret  string `json:"secret"`
}

// CorsConfig 跨域配置
type CorsConfig struct {
	AllowOrigins []string `json:"allow-origins"`
}

type HTTPServerConfig struct {
	ServerName string     `json:"server-name"`
	Listen     string     `json:"listen"`
	Cors       CorsConfig `json:"cors"`
}

type GRPCServerConfig struct {
	ServerName string `json:"server-name"`
}

type DbConfig struct {
	Type    string `json:"type"`
	Dsn     string `json:"dsn"`
	MaxIdle int    `json:"max-idle"`
	MaxConn int    `json:"max-conn"`
	ShowSQL bool   `json:"show-sql"`
}

type RedisConfig struct {
	Addr     string `json:"addr"`
	Password string `json:"password"`
	DB       int    `json:"db"`
}

func GetConfig() AppConfig {
	return appConfig
}
```


