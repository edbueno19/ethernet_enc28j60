set(srcs "enc28j60_example.c")

idf_component_register(SRCS "${srcs}"
                       INCLUDE_DIRS ".")
