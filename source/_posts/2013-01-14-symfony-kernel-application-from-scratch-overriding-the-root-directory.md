---
layout: post
title: Symfony Kernel Application From Scratch: Overriding the Root Directory
date: 2013-01-14 22:31
tags:
    - symfony
    - sculpin

---

> The Kernel is the heart of the Symfony system. It manages an environment made of bundles.
> <footer>— Symfony's Kernel Docblock</footer>

One of the best things that happened to [Sculpin][1] was [Rob Loach][2] suggesting
it support Symfony's Bundle interface for plugins. What started out as an interesting
experiment in trying to bolt Bundles into a completely custom application resulted
in rebuilding Sculpin from scratch with Symfony's Kernel at its core.

Since then I've become a huge fan of Symfony's Kernel. I'm convinced that it makes a
great base for applications of any type.

While things went smoothly for the most part it was not entirely without
pain. I'd like to share some of the things that I've learned in the hopes that a
community can be built around Symfony's Kernel *outside* of the traditional
`HttpKernelInterface` based applications with which people are more familiar.


### Your Root Directory Is Where Now?

One of the design goals for Sculpin was that I wanted to have configuration
(Kernel or otherwise) completely optional. Someone should be able to download
a phar and run it without having to explicitly configure anything.

With respect to Kernel, this meant that I wanted the Kernel configuration
(`app/config/kernel.yml`) and the Kernel class definition (`app/AppKernel.php`)
completely optional. Anyone familiar with Kernel will know that this is going to
be a problem.

The base Kernel implementation uses reflection magic to determine the `kernel.root`
based on the file that defines the Kernel instance. This works when you know
your Kernel is always going to live in `app/AppKernel.php`. This does not work
so well when your Kernel class actually lives deep under `vendor/sculpin/sculpin/src/...`.

This is one of the rare cases that I have run into that Symfony Kernel is actually
tied to how Symfony Standard Edition is setup. Or maybe Symfony Standard Edition is
setup based on this restrction? Either way, there is no way to configure the root
directory; It is always going to be setup by Magic.

Due to the unique way that the Kernel handles setting root directory decorating
Kernel is not an option. It **has** to be done by defining a subclass and it **has**
to be handled in the constructor. This is far from ideal. I had hoped that this
functionality could be added to Symfony core but alas, [it was not to be][3].

But all is not lost. It is possible even if it is not pretty.

    class CustomKernel extends Kernel
    {
        public function __construct($environment, $debug, $rootDir = null)
        {
            if (null !== $rootDir) {
                $this->rootDir = $rootDir;
            }

            parent::__construct($environment, $debug);
        }
    }

While not the prettiest it *does* get the job done. This custom Kernel can now
exist anywhere, whether deep inside `vendor/...` or even embedded in a phar.

We now have a base for building our application around our CustomKernel.

[1]: http://sculpin.io
[2]: http://robloach.net
[3]: https://github.com/symfony/symfony/pull/6337
