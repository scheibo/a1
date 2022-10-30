# a1

![version](http://img.shields.io/badge/version-0.1.0-brightgreen.svg)&nbsp;
[![Build Status](http://img.shields.io/travis/scheibo/a1.svg)](https://travis-ci.org/scheibo/a1)

a1 provides simple authentication and authorization helpers for a single user service in Go.

The generated GoDoc can be viewed at
[godoc.org/github.com/scheibo/a1](https://godoc.org/github.com/scheibo/a1).


## Install

    $ go install github.com/scheibo/a1
    $ a1 password
    $2a$10$LhB2d.LDKkLZG/fdk0Zie.LuThQcM/.B.rZi/GPH08qf0KVd/svFK

## Usage

    func handle(auth *a1.Client) http.Handler {
      return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        path := r.URL.Path
        switch path {
        case "/login":
          switch r.Method {
          case "GET":
            auth.LoginPage().ServerHTTP(w, r)
          case "POST":
            auth.Login().ServeHTTP(w, r)
          default:
            httpError(w, 405)
          }
        case "/logout":
          auth.Logout("/").ServeHTTP(w, r)
        default:
          // auth.CheckXSRF(auth.EnsureAuth(...))
        }
      })
    }

    func main() {
      auth := a1.New(hash)

      srv := &http.Server{
        Addr:         fmt.Sprintf(":%v", port),
        Handler:      a1.RateLimit(10, handle(auth)),
      }
      srv.ListenAndServe()
    }
