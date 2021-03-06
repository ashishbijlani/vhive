# MIT License
#
# Copyright (c) 2020 Plamen Petrov and EASE lab
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

EXTRAGOARGS:=-v -race -cover
CTRDLOGDIR:=/tmp/ctrd-logs
test:
	sudo mkdir -p -m777 -p $(CTRDLOGDIR)
	sudo containerd 1>$(CTRDLOGDIR)/ctrd.out 2>$(CTRDLOGDIR)/ctrd.err &
	sudo env "PATH=$(PATH)" /usr/local/bin/firecracker-containerd --config /etc/firecracker-containerd/config.toml 1>$(CTRDLOGDIR)/fccd.out 2>$(CTRDLOGDIR)/fccd.err &
	sudo ./../vhive 1>$(CTRDLOGDIR)/orch.out 2>$(CTRDLOGDIR)/orch.err &

	./../scripts/cluster/create_one_node_cluster.sh

	sleep 2m

	kubectl apply -f ../knative_workloads/helloworld.yaml
	kubectl apply -f ../knative_workloads/pyaes.yaml
	kubectl apply -f ../knative_workloads/rnn_serving.yaml

	sleep 1m

	go test ./ $(EXTRAGOARGS)
	./../scripts/clean_fcctr.sh

test-travis:
	sudo mkdir -p -m777 -p $(CTRDLOGDIR)
	sudo containerd 1>$(CTRDLOGDIR)/ctrd.out 2>$(CTRDLOGDIR)/ctrd.err &
	sudo env "PATH=$(PATH)" /usr/local/bin/firecracker-containerd --config /etc/firecracker-containerd/config.toml 1>$(CTRDLOGDIR)/fccd.out 2>$(CTRDLOGDIR)/fccd.err &
	sudo ./../vhive 1>$(CTRDLOGDIR)/orch.out 2>$(CTRDLOGDIR)/orch.err &

	./../scripts/cluster/create_one_node_cluster.sh

	sleep 2m

	kubectl apply -f ../knative_workloads/helloworld.yaml

	sleep 2m

	go test $(EXTRAGOARGS) -run TestSingleInvoke
	./../scripts/clean_fcctr.sh

test-man:
	echo "Nothing to test manually"

.PHONY: test test-man
